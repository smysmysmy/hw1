Image_L = imread('lena.bmp');
Image_L = double(Image_L);

% 把lena 512*512图像灰度级逐级递减8-1显示
image1 = Image_L;
for i = 1:7
    image1 = floor(image1./2);
    figure(i)
    imshow(image1,[0,2^(8-i)-1])
end
%计算lena图像的均值方差
average = mean2(Image_L); %均值
var = (std2(Image_L))^2; %方差

%lena图像用近邻、双线性和双三次插值法zoom到2048*2048，并分别保存为lena_nearest，lena_linear，lena_cubic
%自己的方法
A = my_zoom(Image_L,2048,2048,'nearest');
A = uint8(A);
imwrite(A,'lena_nearest.bmp')
A = my_zoom(Image_L,2048,2048,'bilinear');
A = uint8(A);
imwrite(A,'lena_linear.bmp')
A = my_zoom(Image_L,2048,2048,'bicubic');
A = uint8(A);
imwrite(A,'lena_cubic.bmp')
%matlab自带
Image_L = imread('lena.bmp');
Image_LN = imresize(Image_L,[2048 2048],'nearest');
Image_LL = imresize(Image_L,[2048 2048],'bilinear');
Image_LC = imresize(Image_L,[2048 2048],'bicubic');

%把lena和elain图像分别进行水平shear（参数可设置为1.5，或者自行选择）和旋转30度，并采用用近邻、双线性和双三次插值法zoom到2048*2048
Image_E = imread('elain1.bmp');
Image_L = imread('lena.bmp');
T1 = [1,0.5,0;0,1,0;0,0,1];
T2 = [1,0,0;0.5,1,0;0,0,1];
tform1 = affine2d(T1);
tform2 = affine2d(T2);
E_shearH = imwarp(Image_E,tform1);
E_shearV = imwarp(Image_E,tform2);
E_rotate = imrotate(Image_E,30);
%Elain.bmp zoom到2048×2048
E_shearN = imresize(E_shearH,[2048 2048],'nearest');
E_shearL = imresize(E_shearH,[2048 2048],'bilinear');
E_shearC = imresize(E_shearH,[2048 2048],'bicubic');
E_rotateN = imresize(E_rotate,[2048 2048],'nearest');
E_rotateL = imresize(E_rotate,[2048 2048],'bilinear');
E_rotateC = imresize(E_rotate,[2048 2048],'bicubic');

L_shearH = imwarp(Image_L,tform1);
L_shearV = imwarp(Image_L,tform2);
L_rotate = imrotate(Image_L,30);
%lena.bmp zoom到2048×2048
L_shearN = imresize(L_shearH,[2048 2048],'nearest');
L_shearL = imresize(L_shearH,[2048 2048],'bilinear');
L_shearC = imresize(L_shearH,[2048 2048],'bicubic');
L_rotateN = imresize(L_rotate,[2048 2048],'nearest');
L_rotateL = imresize(L_rotate,[2048 2048],'bilinear');
L_rotateC = imresize(L_rotate,[2048 2048],'bicubic');

function image = my_zoom(Raw_Image,length,width,c) %放大函数,Raw_Image为原图像,length为放大后图像宽,width为放大后图像高,c为插值方法
                                                   %'linear'为双线性插值,'cubic'为三次插值
[m,n] = size(Raw_Image); 
Raw_Image = [Raw_Image,Raw_Image(:,n)]; %考虑到边界问题，在原图基础上添加一行一列数据
Raw_Image = [Raw_Image;Raw_Image(m,:)];
[x,y] = meshgrid(1:1:m+1,1:1:n+1);
[x1,y1] = meshgrid(1:n/width:n+1-n/width,1:m/length:m+1-m/length);
image = interp2(x,y,Raw_Image,x1,y1,c);
end