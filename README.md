# cmocean-gmt
cmocean colormaps for Generic Mapping Tools (GMT)

Includes 4 new colormaps from cmocean 2.0!

These colormaps are also available through [cpt-city](http://soliton.vm.bytemark.co.uk/pub/cpt-city/cmocean/index.html).

Bring beautiful oceanography colormaps into your research. There is [documentation](http://matplotlib.org/cmocean/) for the cmocean colormaps.

To use:

Before using the .cpt files for GMT, you must first decide what a good range for your data would be. An example would be for dissolved oxygen, using a range of 0-8 in the Gulf of Mexico. Use whatever range fits your data for your region.

The next step is to use the range that you selected for your data and input it into the .cpt file. Below is a Matlab code that will create the necessary minimum and maximum ranges for you to insert into your .cpt so you can start using them.

```
%Read in selected cmocean-gmt colormap;
[~,Red1,Green1,Blue1,~,Red2,Green2,Blue2]=textread('oxy.txt','%f %f %f %f %f %f %f %f','headerlines',2);

%Adjust this column to fit the span of the dataset
%This sets the scales for the color scale to change
max_oxy=10; %this is the maximum value you want represented on your figure
min_oxy=0; %this is the minimum value you want represented on your figure

d_oxy=(max_oxy-min_oxy)/256; %this divides your range into 256 increments to set up your .cpt file;
%if your .cpt file required 512 lines, you will need to change 256 to 512
range_min=[];
%This loop will create your minimum range column
for i=0:255 %this is for a 256 line .cpt file; change to 511 if using a .cpt with 512 lines
    line2=min_oxy+(i*d_oxy);
    range_min=[range_min line2];
end
range_min=range_min'; %transposes your line from row to column to prepare to print to your .cpt file

range_max=[];
%this loop will create your maximum range column
for ii=0:255
    line2=min_oxy+d_oxy+(ii*d_oxy);
    range_max=[range_max line2];
end
range_max=range_max';

%Select names for your .cpt file and the directory you want your .cpt file
%in
filename1='oxy';
filedir1='/Users/yournamehere/Desktop/color/cpt_files/';

%This prints a cpt file that can be loaded into GMT to plot in specific
%color palette
%NOTE: RGB values must be without decimals in order for cpt to work, so
%must be printed at %4.0f
fid=fopen([filedir1,filename1,'.cpt'],'w'); %opens your .cpt file to write to it
for jj=1:length(Red1)
        fprintf(fid,'%2.2f\t %4.0f\t %4.0f\t %4.0f\t %2.2f\t %4.0f\t %4.0f\t %4.0f \r\n', ...
            range_min(jj),Red1(jj),Green1(jj),Blue1(jj),range_max(jj),Red2(jj),Green2(jj),Blue2(jj));
end
fclose(fid); 
```

Once you have your .cpt file ready, you can now start using it in GMT. Below are a few lines of code from GMT so you can see how the .cpt file is loaded and used with the gmt commands.

```
#Load in .cpt file
oxycpt='./oxycolor.cpt'

#Use -C to use your .cpt file in GMT
awk '{if ($3 > 0 ) {print $1,$2,$3}}' rink.dat | gmt psxy -B0.05:"Latitude (\260N)":/5:"Depth (m)":SWe -R$lat1/$lat2/0/35 -JX5/-3 -C$oxycpt -Sc0.07 -V -K -O

#Use psscale to customized your colorer and add a label
gmt psscale -C$oxycpt -B2.0:"Dissolved Oxygen (ml/L):” -D5.5/1.5/2.5/0.25 -O -V
```
