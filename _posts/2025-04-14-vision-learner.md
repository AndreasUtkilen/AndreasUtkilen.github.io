# Image Classification with fastai

Today I trained my very first deep learning model using fastai—and it was surprisingly smooth! In just a few lines of code, I was able to create an image classifier that can recognize different types of objects.

## What is Image Classification?

Image classification is a computer vision task where the goal is to categorize an image into one of several predefined classes. In this case, we were given five simple categories: airplane, bird, car, cat and dog.

## Getting Started

I began by collecting some images of the objects. There are a few ways to do this:
- Use your own dataset
- Use fastai’s built-in datasets
- Download images from Google using the `duckduckgo_search` tool (fastai provides an example for this)

Here’s a quick snippet I used to download and organize my dataset:

```python
from duckduckgo_search import DDGS
from fastcore.all import *
ddgs = DDGS()
def search_images(term, max_images=200): return L(ddgs.images(term, max_results=max_images)).itemgot('image')


searches = ['airplane', 'bird', 'car', 'cat', 'dog']
for search in searches:
    dest = (path/search.replace(" ", "_"))
    dest.mkdir(exist_ok=True, parents=True)
    download_images(dest, urls=search_images(f'{o} photo'))
    sleep(10)  # Pause between searches to avoid over-loading server
    download_images(dest, urls=search_images(f'{o} sun photo'))
    sleep(10)
    download_images(dest, urls=search_images(f'{o} shade photo'))
    sleep(10)
    for file in glob(f"{dest}/*.fpx"): # remove fpx files which cause problems with resize_images
        os.unlink(file)
    resize_images(path/o, max_size=400, dest=path/o)
```
## Training the Model

Once I had the dataset ready, training the model was almost too easy:

```python
dls = DataBlock(
    blocks=(ImageBlock, CategoryBlock), 
    get_items=get_image_files, 
    splitter=RandomSplitter(valid_pct=0.2, seed=42),
    get_y=parent_label,
    item_tfms=[Resize(192, method='squish')],
).dataloaders(path, bs=64)
learn = vision_learner(dls, resnet18, metrics=error_rate)
learn.fine_tune(3)
```
That’s it! After just a few epochs, I got pretty decent accuracy on my validation set.

## Key Concepts I Learned

- **DataLoaders**: fastai makes it super easy to create training and validation sets using `DataBlock`.
- **Transfer Learning**: `resnet18` is a pretrained model that already understands general features like edges, shapes, and textures. fastai fine-tunes it for your specific dataset.
- **Fine-tuning**: Just a couple of training cycles (`fine_tune(3)`) gave really strong results.

## Results

After training, I ran:

```python
interp = ClassificationInterpretation.from_learner(learn)
interp.plot_confusion_matrix()
```

The confusion matrix showed that most images were correctly classified. However, there were some misclassifications, especially between similar categories like cats and dogs.

## Lessons Learned

- Resize your images before training to avoid memory issues
- Start small—ResNet18 is fast and accurate enough for basic experiments

## What's Next?

- Try using a different architecture (e.g., `resnet34`)
- Collect my own photos and see how the model handles real-world data
