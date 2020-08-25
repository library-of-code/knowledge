## Graphics
GTX 1080

## Driver
nvidia-410 Monday 3rd December 2018

## IIR

```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install nvidia-410
```

## Cuda and Cudnn

### Install Drivers first

Follow Steps above

(Non Compiled Tensorflow Use only)

### Cuda runfile

Download cuda 9.0 from here: https://developer.nvidia.com/cuda-90-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1604&target_type=runfilelocal

### Extract toolkit and samples

```
   cd
   chmod +x cuda_9.0.176_384.81_linux-run
   ./cuda_9.0.176_384.81_linux-run --extract=$HOME
```

### execute toolkit file

```
   sudo ./cuda-linux.9.0.176-22781540.run
```

### run cuda samples installation

```
   sudo ./cuda-samples.9.0.176-22781540-linux.run
```

### Configure runtime library

```
   sudo bash -c "echo /usr/local/cuda/lib64/ > /etc/ld.so.conf.d/cuda.conf"
   sudo ldconfig
```

### Add string to path (Ubuntu only)

```
   sudo vim /etc/environment
   # add /usr/local/cuda/bin to the path
```

### compile the samples

```
   cd /usr/local/cuda-9.0/samples
   sudo make
```

### Check result

```
   cd /usr/local/cuda/samples/bin/x86_64/linux/release
   ./deviceQuery
```

Look for Result = PASS at the end

### Install cuDNN 7.4 for cuda 9.0

Download cuDNN 7.4 for Linux from the nvidia site

move the downloaded archive to /usr/local/

extract the archive

execute ``sudo ldconfig`` and update library cache

