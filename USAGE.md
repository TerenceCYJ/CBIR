## How to run the code?

Let me show you how to use the code.

It can divided to two parts.

### Part1: make your image database
When you clone the repository, it will look like this:

    ├── src/            # Source files
    ├── result/         # Results
    ├── USAGE.md        # How to use the code
    └── README.md       # Intro to the repo

you need to add your images into a directory called __database/__, so it will look like this:

    ├── src/            # Source files
    ├── result/         # Results
    ├── USAGE.md        # How to use the code
    ├── README.md       # Intro to the repo
    └── database/       # Directory of all your images

__all your image should put into database/__

In this directory, each image class should have its own directory

see the picture for details:

<img align='center' style="border-color:gray;border-width:2px;border-style:dashed"   src='https://github.com/brianhuang1019/CBIR/blob/img/database.png' padding='5px'></img>

In my database, there are 25 classes, each class has its own directory,

and the images belong to this class should put into this directory.

### Part2: run each feature extractor
I implement several algorithm, you can run it with python3.

#### For RGB histogram
```python
python3 src/color.py
```

#### For daisy image descriptor
```python
python3 src/daisy.py
```

#### For gabor filter
```python
python3 src/gabor.py
```

#### For edge histogram
```python
python3 src/edge.py
```

#### For histogram of gradient (HOG)
```python
python3 src/HOG.py
```

#### For VGG19
You need to install pytorch0.2 to run the code
```python
python3 src/vggnet.py
```

#### For ResNet152
You need to install pytorch0.2 to run the code
```python
python3 src/resnet.py
```

### Part3: evalutation
Evaluation is an important part, since it tell you how good your feature extraction methods are.

In each feature extractor, you need to implement a method called *histogram*.

Function *histogram* need a image input, and return a vector that represents image's feature.

As soon as you finish Function *histogram*, you can use **evaluate_class** that implemented in [evaluate.py](https://github.com/brianhuang1019/CBIR/blob/master/src/evaluate.py) to evaluate your method.

For example, see [color.py line191](https://github.com/brianhuang1019/CBIR/blob/master/src/color.py#L191)
```python
APs = evaluate_class(Database(), f_class=Color, d_type=d_type, depth=depth)
```
- First parameter is your database
- Parameter *f_class* is your feature extractor Class (need to implement Function *histogram*)
- Parameter *d_type* is the distance metric you want to use
- Parameter *depth* means how many return images for a query image. (depth=5 means to return top-5 images and evaluate on these 5 images)

Then you can print the performance with these codes:
```python
for cls, cls_APs in APs.items():
    MAP = np.mean(cls_APs)
    print("Class {}, MAP {}".format(cls, MAP))
    cls_MAPs.append(MAP)
print("MMAP", np.mean(cls_MAPs))
```

Above are basic usage of my codes.

There are some advanced issue such as features fusion and dimension reduction, see the appendix part below

### Appendix 1: feature fusion
I implement the basic feature fusion method -- concatenation.

Codes for feature fusion is written in [fusion.py](https://github.com/brianhuang1019/CBIR/blob/master/src/fusion.py)

In fusion.py, there is a class called *FeatureFusion*.

You can create a *FeatureFusion* instance with an argument called **features**.

For example, in [fusion.py line140](https://github.com/brianhuang1019/CBIR/blob/master/src/fusion.py#L140)
```python
fusion = FeatureFusion(features=['color', 'daisy'])
APs = evaluate_class(db, f_instance=fusion, d_type=d_type, depth=depth)
```
- The first line means to concatenate color featrue and daisy feature.
- The second line means to evaluate with the concatenated feature.

If you want to know the performance of all possible feature combination, look at [fusion.py line122](https://github.com/brianhuang1019/CBIR/blob/master/src/fusion.py#L122) for example
```python
evaluate_feats(db, N=2, d_type='d1')
```
- Parameter *N* means how many feature you want to concatenate.
- Parameter *d_type* means the distance metric you want to use.
- Function *evaluate_feats* will generate a result file that record performances for all feature concatenation.

## Author
Po-Chih Huang / [@brianhuang1019](http://brianhuang1019.github.io/)
