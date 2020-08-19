# MobiGaze
This work is based on Google's ML kit Sample<br>
https://github.com/googlesamples/mlkit/tree/master/android/vision-quickstart <br>
Collaborators: <br>
<a href="https://github.com/oleeyoung520?tab=repositories">oleeyoung520</a> Email: 2015147520@yonsei.ac.kr <br>
<a href="https://github.com/Yeeun55">Yeeun55</a> Email: joyce9559@naver.com <br>
<a href="https://github.com/yeokyeong46">yeokyeong46</a> Email: yeokyeong46@gmail.com <br>
### Before Cloning to Your Directory
This work requires <b>git-lfs</b> so you must install it first. (tflite file exceeds 100MB...)<br>
After installing git-lfs, on the Directory you want to clone this work, <br>
<pre><code>$ git lfs install
Git LFS initialized.
$ git clone https://github.com/joonb14/MobiGaze.git
</code></pre>
### Summary
I mainly changed <b>FaceDetectorProcessor.java</b> and <b>FaceGraphic.java</b> <br>
Also deleted most of the source code that is not needed<br>
Added custom TensorFlow Lite model which is used for Gaze Estimation<br>
### Gaze Estimation Model
stored in asset folder. Created with Keras, converted to tflite.<br>
named "jw_model.tflite"<br>
You can check the output on Logcat. <b>TAG is "MOBED_GazePoint"</b><br>
### Working on...
MobiGaze uses Personalized model. This example is based on my Data. So would not work well on any other people.<br>
Now working on Calibration. Typically we are going to use 5 points calibration with SVR.<br>
TopLeft, TopRight, BottomLeft, BottomRight, and Center<br>
Since multi output SVR doesn't exist in android, we are using 2 regressors(with android <a href="https://github.com/yctung/AndroidLibSVM">libsvm</a>) for x and y coordinate.<br>
Training Code will be uploaded soon.
### Issues
TensorFlow Lite Conversion. Before you load your tflite model, you must check the input details to make sure input order is correct.<br>
In case you are using python interpreter,
<pre><code>import tensorflow as tf

tflite = tf.lite.Interpreter(model_path="path/to/model.tflite")
tflite.get_input_details()
</code></pre>
example output will be
<pre><code>[{'name': 'left_eye',
  'index': 1,
  'shape': array([  1, 224, 224,   1], dtype=int32),
  'dtype': numpy.float32,
  'quantization': (0.0, 0)},
 {'name': 'right_eye_grid',
  'index': 174,
  'shape': array([ 1, 50, 50,  1], dtype=int32),
  'dtype': numpy.float32,
  'quantization': (0.0, 0)},
 {'name': 'right_eye',
  'index': 173,
  'shape': array([  1, 224, 224,   1], dtype=int32),
  'dtype': numpy.float32,
  'quantization': (0.0, 0)},
 {'name': 'left_eye_grid',
  'index': 2,
  'shape': array([ 1, 50, 50,  1], dtype=int32),
  'dtype': numpy.float32,
  'quantization': (0.0, 0)}]
</code></pre>
Then reorder your inputs in <b>FaceDetectorProcessor.java</b>
<pre><code>float[][][][][] inputs = new float[][][][][]{left_4d, righteye_grid, right_4d, lefteye_grid}; //reorder the parameters
</code></pre>
