# Csapp-preflab

**实验目的：理解编译器，优化程序代码，优化程序运行的速度**

**实验内容：把kernels.c中的rotate和smooth进行优化**



## rotate

主要优化的地方：

1. 因为高速缓存读操作不命中的惩罚比写操作高，又因为空间局部性原则，所以优先在dst数组上以1为步长遍历。

2. 为了消除冗余的运算，我们可以对`RIDX`宏进行拆解，分析可知

   ```
   dst[dim*dim-dim + i - dim*j] == src[dim*i + j]
   ```

   

3. 根据讲义里提示所有图片尺寸为32的倍数，又因为CACHE_BLOCK大小为32，所以我们可对原代码进行32路展开

4. 同样是根据空间局部性原则，尽量使内部循环步长短于外部循环



第一种优化：

消除循环的低效率，减少在循环内的操作次数；
我们可以观察到原先的代码dst[RIDX(dim-1-j,i,dim)] = src[RIDX(i,j,dim)];中的j每变一次，dim-1-j就需要计算一次，共计算了d i m 2 dim^2dim 2次，所以考虑将j换成外层循环变量i，将dim-1-i计算减少到dim次，直接写成dst[RIDX(i,j,dim)] = src[RIDX(j,dim-1-i,dim)];即可



```
void rotate(int dim, pixel *src, pixel *dst)
{
    // dst = dim*dim-dim + i - dim*j
    // src = dim*i + j
    int i,j;
    dst+=(dim*dim-dim);
    for(i=0;i<dim;i+=32){
        for(j=0;j<dim;j++){
            dst[0]=src[0];
            dst[1]=src[dim];
            dst[2]=src[2*dim];
            dst[3]=src[3*dim];
            dst[4]=src[4*dim];
            dst[5]=src[5*dim];
            dst[6]=src[6*dim];
            dst[7]=src[7*dim];
            dst[8]=src[8*dim];
            dst[9]=src[9*dim];
            dst[10]=src[10*dim];
            dst[11]=src[11*dim];
            dst[12]=src[12*dim];
            dst[13]=src[13*dim];
            dst[14]=src[14*dim];
            dst[15]=src[15*dim];
            dst[16]=src[16*dim];
            dst[17]=src[17*dim];
            dst[18]=src[18*dim];
            dst[19]=src[19*dim];
            dst[20]=src[20*dim];
            dst[21]=src[21*dim];
            dst[22]=src[22*dim];
            dst[23]=src[23*dim];
            dst[24]=src[24*dim];
            dst[25]=src[25*dim];
            dst[26]=src[26*dim];
            dst[27]=src[27*dim];
            dst[28]=src[28*dim];
            dst[29]=src[29*dim];
            dst[30]=src[30*dim];
            dst[31]=src[31*dim];
            src++;      //j++ => src+=1
            dst-=dim;   //j++ => dim+=-dim
        }    
        //i+=32 => src+=32*dim, then neutralize the effects of for(j)
        src+=31*dim;
        //i+=32 => dst+=32, then neutralize the effects of for(j)
        dst+=dim*dim+32;
    }
}
```



运行一下，看一下结果

```
make
./driver
```

```
Rotate: Version = naive_rotate: Naive baseline implementation:
Dim             64      128     256     512     1024    Mean
Your CPEs       2.8     4.2     5.3     10.6    11.5
Baseline CPEs   14.7    40.1    46.4    65.9    94.5
Speedup         5.2     9.4     8.8     6.2     8.2     7.4

Rotate: Version = rotate: Current working version:
Dim             64      128     256     512     1024    Mean
Your CPEs       2.7     2.2     2.2     2.7     4.2
Baseline CPEs   14.7    40.1    46.4    65.9    94.5
Speedup         5.4     18.0    21.0    24.8    22.6    16.3
```



## smooth

1. avg中有大量的冗余的max和min函数调用，可通过分类讨论四角、四边、中间的边界条件来优化之。
2. src的每个单元格都被多次读取，利用效率不高，可以通过复用读取的值来减少读取次数。





```
pixel_sum p_sum[512][512];
static void three_pixel_sum(pixel_sum *sum, pixel a, pixel b, pixel c)
{
    sum->red=(int)(a.red+b.red+c.red);
    sum->green=(int)(a.green+b.green+c.green);
    sum->blue=(int)(a.blue+b.blue+c.blue);
}
static void two_pixel_sum(pixel_sum *sum, pixel a, pixel b){
    sum->red=(int)(a.red+b.red);
    sum->blue=(int)(a.blue+b.blue);
    sum->green=(int)(a.green+b.green);
}
static void add_pixel_sum(pixel_sum *a, pixel_sum b){
    a->red+=b.red;
    a->green+=b.green;
    a->blue+=b.blue;
}
static void sum2pixel(pixel *current_pixel, pixel_sum sum, int num)
{
    current_pixel->red = (unsigned short)(sum.red / num);
    current_pixel->green = (unsigned short)(sum.green / num);
    current_pixel->blue = (unsigned short)(sum.blue / num);
    return;
}
void smooth(int dim, pixel *src, pixel *dst)
{
    pixel_sum sum;
    int r,c;
    int dimsubone=dim-1;
    //初始化
    for(r=0;r<dim;r++){
        for(c=0;c<dim;c++){
            initialize_pixel_sum(&p_sum[r][c]);
        }
    }
    //计算中间部分
    for(r=1;r<dimsubone;r++){
        for(c=1;c<dimsubone;c++){
            three_pixel_sum(&sum,src[RIDX(r,c-1,dim)],src[RIDX(r,c,dim)],src[RIDX(r,c+1,dim)]);
            add_pixel_sum(&p_sum[r-1][c],sum);
            add_pixel_sum(&p_sum[r][c],sum);
            add_pixel_sum(&p_sum[r+1][c],sum);
        }
    }
    //计算上下两边
    for(c=1;c<dimsubone;c++){
        three_pixel_sum(&sum,src[RIDX(0,c-1,dim)],src[RIDX(0,c,dim)],src[RIDX(0,c+1,dim)]);
        add_pixel_sum(&p_sum[0][c],sum);
        add_pixel_sum(&p_sum[1][c],sum);
        three_pixel_sum(&sum,src[RIDX(dimsubone,c-1,dim)],src[RIDX(dimsubone,c,dim)],src[RIDX(dimsubone,c+1,dim)]);
        add_pixel_sum(&p_sum[dim-2][c],sum);
        add_pixel_sum(&p_sum[dimsubone][c],sum);
    }
    //计算左右两边
    for(r=1;r<dimsubone;r++){
        two_pixel_sum(&sum,src[RIDX(r,0,dim)],src[RIDX(r,1,dim)]);
        add_pixel_sum(&p_sum[r-1][0],sum);
        add_pixel_sum(&p_sum[r][0],sum);
        add_pixel_sum(&p_sum[r+1][0],sum);
        two_pixel_sum(&sum,src[RIDX(r,dim-2,dim)],src[RIDX(r,dimsubone,dim)]);
        add_pixel_sum(&p_sum[r-1][dimsubone],sum);
        add_pixel_sum(&p_sum[r][dimsubone],sum);
        add_pixel_sum(&p_sum[r+1][dimsubone],sum);
    }
   //计算四角
  two_pixel_sum(&sum,src[RIDX(0,0,dim)],src[RIDX(0,1,dim)]);
    add_pixel_sum(&p_sum[0][0],sum);
    add_pixel_sum(&p_sum[1][0],sum);
    two_pixel_sum(&sum,src[RIDX(0,dim-2,dim)],src[RIDX(0,dimsubone,dim)]);
    add_pixel_sum(&p_sum[0][dimsubone],sum);
    add_pixel_sum(&p_sum[1][dimsubone],sum);
    two_pixel_sum(&sum,src[RIDX(dimsubone,0,dim)],src[RIDX(dimsubone,1,dim)]);
    add_pixel_sum(&p_sum[dim-2][0],sum);
    add_pixel_sum(&p_sum[dimsubone][0],sum);
    two_pixel_sum(&sum,src[RIDX(dimsubone,dim-2,dim)],src[RIDX(dimsubone,dimsubone,dim)]);
    add_pixel_sum(&p_sum[dim-2][dimsubone],sum);
    add_pixel_sum(&p_sum[dimsubone][dimsubone],sum);
    //中部有9个相邻点
    for(r=1;r<dimsubone;r++){
        for(c=1;c<dimsubone;c++){
            sum2pixel(&dst[RIDX(r,c,dim)],p_sum[r][c],9);
        }
        sum2pixel(&dst[RIDX(r,0,dim)],p_sum[r][0],6);
        sum2pixel(&dst[RIDX(r,dimsubone,dim)],p_sum[r][dimsubone],6);
    }
    //四边有6个相邻点
    for(c=1;c<dimsubone;c++){
        sum2pixel(&dst[RIDX(0,c,dim)],p_sum[0][c],6);
        sum2pixel(&dst[RIDX(dimsubone,c,dim)],p_sum[dimsubone][c],6);
    }
    //四角有4个相邻点
    sum2pixel(&dst[RIDX(0,0,dim)],p_sum[0][0],4);
    sum2pixel(&dst[RIDX(dimsubone,0,dim)],p_sum[dimsubone][0],4);
    sum2pixel(&dst[RIDX(0,dimsubone,dim)],p_sum[0][dimsubone],4);
    sum2pixel(&dst[RIDX(dimsubone,dimsubone,dim)],p_sum[dimsubone][dimsubone],4);

}
```



运行一下

```
make
./driver
```



```
Smooth: Version = naive_smooth: Naive baseline implementation:
Dim             32      64      128     256     512     Mean
Your CPEs       52.5    50.2    50.6    52.0    51.7
Baseline CPEs   695.0   698.0   702.0   717.0   722.0
Speedup         13.2    13.9    13.9    13.8    14.0    13.8

Smooth: Version = smooth: Current working version:
Dim             32      64      128     256     512     Mean
Your CPEs       28.8    29.4    29.6    30.6    32.3
Baseline CPEs   695.0   698.0   702.0   717.0   722.0
Speedup         24.1    23.7    23.8    23.4    22.3    23.5
```

