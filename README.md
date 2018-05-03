# image-restoration
#image destoration using pseudo-inverse and wiener methods 
clc 
clear
close all
cd 5
%% read image
[pic, path] = uigetfile('*.tif');
pic = imread(num2str(pic));
row=size(pic,1);
col=size(pic,2);

%[m , n] = size(pic) %%m=563 n=623

%%  creat a pic with salib
mypic = zeros(row,col); %get Heart image with Destruction
mypic(end-62:end-32,end-49:end-46) = 255; 
mypic(end-49:end-46,end-62:end-32) = 255; 
subplot(3,2,1);imshow(mypic)
title('salib in new img');
%% get salib from destruction image
[pic1, pathname] = uigetfile('*.tif'); 
cd ..
pic1 = imread(num2str(pic1));
%% 
pic1(1:end-75,1:end)=0; 
pic1(1:end,1:end-75)=0; 
%%
pic1=uint8(pic1);
subplot(3,2,2);imshow(pic1)
title('salib from org img');
%%  get destortion function in fft

F=fft2(mypic);%fourie function F
G=fft2(pic1); %fourie function G
H = G./F ;
subplot(3,2,3);imshow(H)
title('destortion function');
%% show  org image with H function
pic_fft =fft2(pic) ;% foriye of org image
F_fft=pic_fft./H ; %F=G/H
F_ifft=ifft2(F_fft);
F_ifft=uint8(F_ifft);
subplot(3,2,4);imshow(F_ifft)
title('org image function');
%%  pseudo_inverse Filter
  k=60;
 for i=1:row
  for j=1:col 
   P_invers= sqrt((j-col/2)^2+(i-row/2)^2);  %lowpass filter
   Lp(i,j)=double(P_invers <= k);         
  
  end 
 end 
fft_img=fftshift(F_fft) ;
 F_p=Lp.*fft_img;
 F_p=ifftshift(F_p) ;
 F_p=ifft2(F_p); 
subplot(3,2,5);imshow(uint8(F_p))
title(' P_Inverse filter');

%% weiener
K=0.032;
wie_F = (1./H).*((abs(H)).^2./((abs(H)).^2 + K)).*pic_fft;
wie_f=ifft2(wie_F);
wie_f=uint8(wie_f);
subplot(3,2,6);imshow(wie_f)
title(' wiener filter2 ');
