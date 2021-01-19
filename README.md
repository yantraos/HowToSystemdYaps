# HowToSystemd

sudo rm /bin/sh
sudo ln -s /bin/bash /bin/sh
sudo apt-get install bison -y
sudo apt-get install gawk -y
sudo rm /usr/bin/awk 
sudo ln -s /usr/bin/gawk /usr/bin/awk
sudo apt-get install gcc g++ make -y
sudo apt-get install texinfo -y
sudo mkfs -v -t ext4 /dev/sda7
sudo mkswap /dev/sda6
export yantra=/mnt/yantra
sudo mkdir -pv $yantra
sudo mkdir -pv $yantra    
sudo mount -v -t ext4 /dev/sda7 $yantra
sudo /sbin/swapon -v /dev/sda6

yantra=/mnt/yantra
LFSVERSION=9.0
SOURCEARCHIVES=/ysarchives/$LFSVERSION
BUILTPACKAGES=/ybpackages/$LFSVERSION
EXTFOLDER=
KERNELVERSION=5.2.8
USESYSTEMD=1
export yantra
export LFSVERSION
export SOURCEARCHIVES
export BUILTPACKAGES
export EXTFOLDER
export KERNELVERSION	

cd $yantra

chown -R $USER:$USER $yantra/ybuild
ln -sv ybuild/yinsource $yantra/yinsource
ln -sv ybuild/yinscripts $yantra/yinscripts
cd $yantra/yinscripts
./ysetup


./ShowSystemData

./BuildTools-SystemD


. ./SystemData
mkdir -vp ${yantra}/${SOURCEARCHIVES}||true 
mkdir -vp ${yantra}/${BUILTPACKAGES}||true 
if [ "X$EXTFOLDER" != "X" ];then
sudo mount --bind $EXTFOLDER/${BUILTPACKAGES} $yantra/${BUILTPACKAGES}
sudo mount --bind $EXTFOLDER/${SOURCEARCHIVES} $yantra/${SOURCEARCHIVES} 
fi



./BuildFileSystem-SystemD
sudo chown -R $USER:$USER $yantra/ybuild $yantra/ysarchives/$LFSVERSION $yantra/ybpackages/$LFSVERSION
chmod -R 755 $yantra/ysarchives/$LFSVERSION $yantra/ybpackages/$LFSVERSION
mkdir  $yantra/ysarchives/$LFSVERSION/KERNEL
cd $yantra/ysarchives/$LFSVERSION/KERNEL
ln -sv ../SYSTEM/linux-$KERNELVERSION.tar.xz .
cd $yantra/yinsource/admin/yinstal
wget -c --no-check-certificate "https://github.com/yantraos/yinstal/blob/master/yinstal-master.zip"  -O yinstal-master.zip
unzip yinstal-master.zip
cd $yantra/yinscripts
sudo  ./Chroot system
cd /yinsource/admin/yinstal
BOOTSTRAP=1 ./yinstal.ybuild install
yaps --setup
cd /yinsource/system/system-meta
./system-meta.ybuild install
