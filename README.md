# TensorFlow and TF-TRT benchmarks

Setup details and notebooks for
the benchmarks I ran on the Raspberry Pi 4 and Jetson Nano.

## Setup

The notebooks were based on [this excellent article](https://www.dlology.com/blog/how-to-run-keras-model-on-jetson-nano/).

### On my workstation

I started by following Chengwei Zhang's recipe.
I trained the model on my workstation using 
and then copied `trt_graph.pb` from my workstation to the Pi 4.


### Raspberry Pi 4

I used a virtual environment created with pipenv, and installed jupyter and pillow.

I downloaded and installed [this unofficial wheel](https://github.com/PINTO0309/Tensorflow-bin/blob/master/tensorflow-1.14.0-cp37-cp37m-linux_armv7l.whl).

I tried to run `step2.ipynb` and encountered an import error.
This turned out to be an old TensorFlow bug resurfacing.

The maintainer of the wheel will fix the problem when time permits,
but I used a simple workaround.

I used `cd pipenv --venv` to go to the location of the virtual environment,
and then ran `cd lib/python3.7/site-packages/tensorflow/contrib/` to move to the location
of the offending file `__init__.py`

The problem lines are
 
    if os.name != "nt" and platform.machine() != "s390x":
      from tensorflow.contrib import cloud

These try to import `cloud` from tensorflow.contrib, which isn't there
and fortunately isn't needed :)
    
I replaced the second line with `pass` using

    sed -i  '/from tensorflow.contrib import cloud/s/^/    pass # commented out - RJC >>>/' __init__.py

and captured the timings.

Later, I ran `raw-mobile-netv2.ipynb` to see how long it took to run
the training session, and to save the model and frozen graph on the Pi.

### Jetson Nano

I used the Nano that I had configured for my series on
[Getting Started with the Jetson Nano](https://blog.rareschool.com/2019/05/getting-started-with-jetson-nano.html);
it had tensorflow, pillow and jupyer lab installed.

I found that I could not load the saved/imported `trt_graph.pb` on the Nano.

Since running the original training stage on the Pi
did not take as long as I'd expected,
I ran `step1.ipynb` on the nano and used the locally created `trt_graph.pb`
file which loaded OK.

Then I ran `step2.ipynb` and captured the timings which I published.




    
    


