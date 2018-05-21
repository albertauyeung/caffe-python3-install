# install py-faster-rcnn with python3

### install caffe

Check the [guide](https://github.com/dungba88/caffe-python3-install/blob/master/install-caffe.md) for complete instruction. 

But there are some changes needed to run py-faster-rcnn. You will need to merge commit [0dcd397b29507b8314e252e850518c5695efbb83](https://github.com/rbgirshick/caffe-fast-rcnn/commit/0dcd397b29507b8314e252e850518c5695efbb83) after checking out the caffe code.

```shell
git remote add caffe-fast-rcnn git://github.com/rbgirshick/caffe-fast-rcnn.git
git fetch caffe-fast-rcnn
git cherry-pick -n 0dcd397b29507b8314e252e850518c5695efbb83
```

Another change required to fix `AttributeError: can't set attribute` in caffe.Net is patched here https://github.com/andpol5/caffe/commit/1bbcb0605fe67525a8493fb477d30119893a0978

### install py-faster-rcnn (without GPU support)

Checkout from https://github.com/dungba88/py-faster-rcnn. It includes fixes for Python3 compatibility.

Make the following changes for CPU only
- in lib/setup.py, uncomment `CUDA = locate_cuda()` and the `nms.gpu_nms` extension
- in lib/fast-rcnn/config.py set `USE_GPU_NMS` to False

Then follow the rest of the original guide!


## Notes

### CUDA lib64 path

When building the Cython module, the following error is encountered:

```shell
python setup.py build_ext --inplace
Traceback (most recent call last):
  File "setup.py", line 58, in <module>
    CUDA = locate_cuda()
  File "setup.py", line 55, in locate_cuda
    raise EnvironmentError('The CUDA %s path could not be located in %s' % (k, v))
OSError: The CUDA lib64 path could not be located in /usr/lib64
Makefile:2: recipe for target 'all' failed
make: *** [all] Error 1
```

To solve this problem, in `~/computer_vision/py-faster-rcnn/lib/setup.py` change:

```
cudaconfig = {'home':home, 'nvcc':nvcc,
              'include': pjoin(home, 'include'),
              'lib64': pjoin(home, 'lib64')}
```

to:

```
cudaconfig = {'home':home, 'nvcc':nvcc,
              'include': pjoin(home, 'include'),
              'lib64': pjoin(home, 'lib')}
```


### hdf5 related errors

In `Makefile.config`, change:

```
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
```

to

```
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/
```

And in `Makefile`, change:

```
LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_hl hdf5
```

to

```
LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_serial_hl hdf5_serial
```

