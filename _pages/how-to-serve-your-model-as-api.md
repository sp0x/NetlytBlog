---
layout: post
title:  "How to serve your ML model as an API"
date:   2019-02-27 13:16:42  +0200
categories: how-to
description: How to use Flask with your machine learning model in order to turn it into a lightweight API. 
---

# Creating an API for your NLP model.

We @Netlyt have noticed that there's a barrier between some businesses and the power of machine learning, so we're trying to help everybody by making it really easy to get actionable insight and predictions from your data.

Netlyt specializes in solving complex AI problems. Ranging from energy problems in Africa to predicting air pollution in Europe, our efforts are focused on solving real-world problems and providing prototypes in a matter of weeks.  
[Contact us now](https://netlyt.io/#contact)  

**In this post you'll learn how to create a simple API from a machine learning model, using Python and Flask**

Assuming that you've built a nice machine learning model, that could recognize the language of any text.
How can you or anybody else use your model from other languages, like Java for example?

Fortunately enough you can use the power of APIs. With them, your model could be used by any language or platform that can make a simple HTTP request.
Nowadays many industries are looking for Data Scientists who can do this.

Wrapping a machine learning model into an API is quite easy, and that's exactly what you'll see in this post.  
These are the steps that we'll go through:
- Options for implementing models  
- Flask basics  
- Creating a machine learning model  
- Saving and loading the model  
- Creating an API that would use the model, using Flask  
- Testing your API in Postman  
- Future steps that you can take

## Options for implementing models
Most often the majority of ML people use R/Python(Jupyter) for their experiments.
But the consumer of those models would be software engineers or data engineers, who use a different technology stack.
This difference can often create issues for the consumers of the models.
These issues could be resolved by either recreating the model in the language that the consumer would use or by wrapping the model in an API/service.  
The first option might seem like a good option, but the time required to implement this would be a waste.
The latter approach is an easy way to make cross-language applications work.

## Flask - A minimal web framework in Python:
Flask is a minimal web service development framework in Python.

If you've downloaded the Anaconda distribution, you already have Flask installed.
Otherwise, you'll need to install it.
```
pip install flask
```
Here's a basic hello world web server in Flask:
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
  return 'Hello world!'

if __name__ == '__main__':
  app.run(debug=True)
```

Run it with
```python
python app.py
```

Let's go over the content of app.py.  
- We created an instance of the Flask class, passing the name variable which would be `main`
- `@app.route("/")` is a decorator, that tells Flask that it should call the `index` function, whenever the `/` route is required.
- The return value of `index` is then written to the response's body.

## Creating a machine learning model
Jupyter notebook is a great tool for anything related to machine learning.
Frameworks like scikit-learn, pytorch, tensorflow and others make it really easy for you to create models.
The first step when you're picking a model that you want to create is to figure out if it would be a regression or a classification model.
Categorizing your problem:
1. **By input**
- If you have labeled data, it's a supervised learning problem
- If you have unlabelled data, it's an unsupervised learning problem.
- If you want to optimize a function by interacting with an environment, it's a reinforced learning problem.

2. **By output**
- If the output of the model is a number, it's a regression problem
- If the output of your model is a class, it's a classification problem
- If the output of your model is a set of input groups, it's a clustering problem


## Saving and loading models:
Machine learning models can be serialized to files which save you time from retraining your model. 
Note that it's important to freeze your dependencies with pip because your models might break if the version of your dependencies changes.
If you miss out this step your models wouldn't be truly portable.

### Data formatting:
Most often your models require the input data to be in numerical format. So you need to preprocess your data, and eliminating any text fields from it.
You could use a few methods to do that: 
- LabelEncoder
- OneHotEncoder from sklearn.preprocessing
- Word2vec from gensim

You also need to make sure you don't have any missing or null values.
To fill them in you can use `SimpleImputer` from `sklearn.impute`

## Saving the model
You have a few ways of doing this:  
Sklearn's joblib:
You can use the joblib utility from Sklearn in order to saver models
```python
from sklearn.externals import joblib
joblib.dump(modelObj, 'model.pkl')
```
**Pickle**
```python
import pickle
with open('model.pkl', 'wb') as f:
  pickle.dump(modelObj, f)
```
**Tensorflow**
```python
saver = tf.train.Saver()
with tf.Session() as sess:
  ##... do the actual training with `sess`
  saver.save(sess, "model.ckpt", global_step=global_step)
```

### Loading the model
**Scikit's Joblib:**
```python
model = joblib.load("model.pkl")
```
**Pickle**
```python
import pickle
with open("model.pkl", 'rb') as f:
  model = pickle.load(f)
```
**Tensorflow**
```python
saver = tf.train.Saver()
with tf.Session() as sess:
  saver.restore(sess, "model.ckpt")
  #Do the prediction with the restored model i n this session
```

## Creating the API with Flask
Here we'll use [this repository](https://github.com/sp0x/flask_langdetect) as an example.
It uses a pre-trained tensorflow model to predict the language of any text.  
You can look at `app.py` to see the API implemented.

To serve your model using Flask, you need to do a few things:
- Load any configuration or model that's saved when the app starts.
- Preprocess your input if it's needed
- Create an API endpoint that takes your input, and guesses the output.

Here's an example of what our input is:
```json
[
  "Test this language", "Some more text to guess it's language", "慢走。"
]
```
And our output from the API would look like this:
```json
[
  "en", "en", "zh-tw"
]
```
Each element in the output is the language of the same-indexed element in the input.  
Note that our last element in the input is in Chinese or Taiwanese Mandarin.

Our flask API looks as simple as this:
```python
from flask import Flask, jsonify, request
app = Flask(__name__)


@app.route('/predict', methods=['POST'])
def serve():
    try:
        inpjson = request.json
        prediction = list(predict_batch(inpjson))
        output = jsonify(prediction)
        return output
    except:
        return jsonify({'trace': traceback.format_exc()})


if __name__ == "__main__":
    app.run(port=8989, debug=True)
```
To run the API just run `python app.py`, which might give you something similar to this
```log
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 930-075-557
 * Running on http://127.0.0.1:8989/ (Press CTRL+C to quit)
```


## Testing your API in Postman
You'll need some form of an HTTP client, in order to call the API. This could be curl or any utility that you use to make HTTP requests in any language.  
Postman is a simple API client that lets you test out your APIs.  
To try it out install Postman and send a **POST** request to **http://localhost:8989/predict**  
With a body like this:
```json
[
    "Test this language", "Some more text to guess it's language", "慢走。"    
]
```
After pressing `Send` you'll see the output of the response, which should contain the predicted languages.

## Future steps that you can take
This post covered the basics of creating an API for your model.
There are some additional steps that might improve your API:
- Caching: Implement prediction caching on some layer, either by putting an Nginx server in front of your API or using a DB like Redis to cache predictions
- Training: You'll need to make changes to your model as data changes, so it's always up to date, which means you'll need to retrain as time goes on. 
- Hosting: You can host your API on AWS or Google's GCP so it could be consumed.
- Benchmarking: you should benchmark your model and figure out its performance requirements.

In the next post, we'll cover [How to turn your model into a microservice](#) so it's easy to deploy and scale. 
To see the full code for this post, [check out this repository](https://github.com/sp0x/flask_langdetect).

