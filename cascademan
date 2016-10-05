#!/usr/bin/python
####################################################################
# cascademan - a manager for cascade classifiers in openCV         #
#                                                                  #
####################################################################

######################  COLORS  ######################
# Text attributes
END = 0
BOLD = 1
SPECIAL = 2
ITALIC = 3
UNDERLINE = 4
REVERSE = 7
CONCEALED = 8
STRIKE = 9

# Foreground colors
BLACK = 30
D_RED = 31
D_GREEN = 32
D_YELLOW = 33
D_BLUE = 34
D_MAGENTA = 35
D_CYAN = 36
GRAY = 37

D_GRAY = 90
RED = 91
GREEN = 92
YELLOW = 93
BLUE = 94
MAGENTA = 95
CYAN = 96

# Background colors
B_BLACK = 40
B_D_RED = 41
B_D_GREEN = 42
B_D_YELLOW = 43
B_D_BLUE = 44
B_D_MAGENTA = 45
B_D_CYAN = 46
B_L_GRAY = 47

B_GRAY = 100
B_RED = 101
B_GREEN = 102
B_YELLOW = 103
B_BLUE = 104
B_MAGENTA = 105
B_CYAN = 106
B_WHITE = 107

def code(i):
    return '\033[' + str(i) + 'm'

endCode = code(END)

def color(text, color):
    mycode = code(color)
    return mycode + str(text).replace(endCode, endCode + mycode) + endCode

def bold(t): return color(t, BOLD)
def special(t): return color(t, SPECIAL)
def italic(t): return color(t, ITALIC)
def uline(t): return color(t, UNDERLINE)
def rev(t): return color(t, REVERSE)
def concealed(t): return color(t, CONCEALED)
def strike(t): return color(t, STRIKE)

def black(t): return color(t, BLACK)
def Dred(t): return color(t, D_RED)
def Dgreen(t): return color(t, D_GREEN)
def Dyellow(t): return color(t, D_YELLOW)
def Dblue(t): return color(t, D_BLUE)
def Dmagenta(t): return color(t, D_MAGENTA)
def Dcyan(t): return color(t, D_CYAN)
def gray(t): return color(t, GRAY)

def Dgray(t): return color(t, D_GRAY)
def red(t): return color(t, RED)
def green(t): return color(t, GREEN)
def yellow(t): return color(t, YELLOW)
def blue(t): return color(t, BLUE)
def magenta(t): return color(t, MAGENTA)
def cyan(t): return color(t, CYAN)

def Bblack(t): return color(t, B_BLACK)
def BDred(t): return color(t, B_D_RED)
def BDgreen(t): return color(t, B_D_GREEN)
def BDyellow(t): return color(t, B_D_YELLOW)
def BDblue(t): return color(t, B_D_BLUE)
def BDmagenta(t): return color(t, B_D_MAGENTA)
def BDcyan(t): return color(t, B_D_CYAN)
def BLgray(t): return color(t, B_L_GRAY)

def Bgray(t): return color(t, B_GRAY)
def Bred(t): return color(t, B_RED)
def Bgreen(t): return color(t, B_GREEN)
def Byellow(t): return color(t, B_YELLOW)
def Bblue(t): return color(t, B_BLUE)
def Bmagenta(t): return color(t, B_MAGENTA)
def Bcyan(t): return color(t, B_CYAN)
def Bwhite(t): return color(t, B_WHITE)
######################  END COLORS  ######################

import json, os, glob, filecmp, shutil, sys, cv2, random, xmltodict, readline

#uses json to store a dict of settings
class CfgFile(object):
  def __init__(self, filename):
    self.filename = filename
    self.load()
  def load(self):
    try:
      with open(self.filename, 'r') as f:
        self.contents = json.load(f)
    except FileNotFoundError:
      self.contents = {}
  def dump(self):
    with open(self.filename, 'w') as f:
      json.dump(self.contents, f, sort_keys=True, indent=4, separators=(',', ': '))
  def __repr__(self):
    return json.dumps(self.contents, sort_keys=True, indent=4, separators=(',', ': '))
  def __len__(self):
    return len(self.contents)
  def __setitem__(self, key, value):
    self.contents[key] = value
  def __getitem__(self, key):
    return self.contents[key]
  def __delitem__(self, key):
    del self.contents[key]
  def __contains__(self, item):
    return item in self.contents

class Path(str):
  def __new__(cls, path):
    obj = str.__new__(cls, path)
    obj.p = path
    return obj
#  def __init__(self, path):
#    self.p = path

  def createDir(self):
    if not self.exists:
      os.makedirs(self.p)
  def createFile(self):
    parent = self.dirname #os.path.dirname(self.p)
    if not parent.exists:
      os.makedirs(parent)
    open(self.p, 'a').close() #create the file if it doesn't exist

  @property
  def path(self):
    return self.p

  @path.setter
  def path(self, value):
    self.p = value

  @property
  def dirname(self):
    return Path(os.path.dirname(self.p))

  @property
  def basename(self):
    return os.path.basename(self.p)

  @property
  def extension(self):
    return os.path.splitext(self.p)[1].lstrip('.')

  @property
  def basenameNoExt(self):
    return os.path.splitext(os.path.basename(self.p))[0]

  @property
  def exists(self):
    return os.path.exists(self.p)

  @property
  def isFile(self):
    return os.path.isfile(self.p)

  @property
  def isDir(self):
    return os.path.isdir(self.p)

  @property
  def ls(self):
    if self.isDir:
      return sorted([self + f for f in os.listdir(self.p)])
    elif self.isFile:
      return [self]
    else:
      return []

  #search for files with certain extensions recursively in a directory
  def findFilesByExt(self, *extensions):
    if self.isFile:
      if self.extension in extensions:
        return [self]
      else:
        return []
    files = []
    for ext in extensions:
      files.extend(glob.iglob(self.p + '/**/*.' + ext, recursive=True))
    files = [Path(f) for f in files]
    return files

  def hasDuplicate(self, filename):
    for filename2 in self.ls:
      if filecmp.cmp(filename, filename2):
        return True
    return False

  def __repr__(self): #used for string conversion
    return self.p
  def __lt__(self, other):
    return self.p.__lt__(other.p)
  def __add__(self, other):
    if isinstance(other, str):
      return Path(os.path.join(self.p, other))
    elif isinstance(other, Dir):
      return Path(os.path.join(self.p, other.p))
  def __radd__(self, other):
    if isinstance(other, str):
      return Path(os.path.join(other, self.p))
    elif isinstance(other, Dir):
      return Path(os.path.join(other.p, self.p))

def yesOrNoPrompt(message):
  while True:
    sys.stdout.write(bold(yellow(message + " (y/n)")))
    answer = input()
    if answer.lower() in ['y', 'yes']:
      return True
    elif answer.lower() in ['n', 'no']:
      return False
    else:
      print(red("Enter y or n."))

#display a progress bar on the screen
class ProgressBar(object):
  def __init__(self, ticks=40, x=0):
    self.width = ticks
    self.x = x
    sys.stdout.write("[%s]" % (" " * self.width))
    sys.stdout.flush()
    sys.stdout.write("\b" * (self.width+1)) # return to start of line, after '['
  def tick(self):
    if self.x < self.width:
      self.x += 1
      sys.stdout.write("-")
      sys.stdout.flush()
    if self.x == self.width:
      self.x += 1
      sys.stdout.write("\n")
  def __repr__(self):
    return "{}/{}".format(self.x, self.width)

class Category(object):
  def all(parentDir):
    return [Category(parentDir, f.basename) for f in parentDir.ls]

  def __init__(self, parentDir, name):
    self.name = name
    self.p = parentDir + name
    self.imgDir = self.p + "images"
    self.cropInfoDir = self.p + "crop_info"
    self.trainingDir = self.p + "training"
    self.resultsDir = self.p + "results"

  @property
  def exists(self):
    return self.p.exists

  def create(self):
    self.imgDir.createDir()
    self.cropInfoDir.createDir()
    self.trainingDir.createDir()
    self.resultsDir.createDir()

  def delete(self):
    shutil.rmtree(self.p)

  @property
  def path(self):
    return self.p

  @property
  def images(self):
    return self.imgDir.ls

  @property
  def numImages(self):
    return len(self.images)

  def rename(self, newName):
    parentDir = self.p.dirname
    newPath = parentDir + newName
    if not self.exists:
      print(red("Category '{}' does not exist.".format(self)))
      return False
    if newPath.exists:
      if yesOrNoPrompt("Category '{}' exists. Overwrite?".format(newName)):
        shutil.rmtree(newPath)
      else:
        return False
    shutil.move(str(self.p), str(newPath))
    print(green("Category '{}' has been renamed to '{}'.".format(self.name, newName)))
    self.name = newName
    self.p = newPath
    self.imgDir = self.p + "images"
    return True

  def copy(self, newName):
    parentDir = self.p.dirname
    newPath = parentDir + newName
    if not self.exists:
      print(red("Category '{}' does not exist".format(self)))
      return None
    if newPath.exists:
      if yesOrNoPrompt("Category '{}' exists. Overwrite?".format(newName)):
        shutil.rmtree(newPath)
      else:
        return None
    shutil.copytree(str(self.p), str(newPath))
    print(green("Category '{}' has been copied to '{}'.".format(self.name, newName)))
    return Category(parentDir, newName)

  #add images to a category
  def add(self, sources, silent=False):
    files = []
    #loop through the image source directories
    for src in sources:
      #and find all the images within
      files.extend(Path(src).findFilesByExt('jpg', 'jpeg', 'png', 'JPG', 'JPEG', 'PNG'))
    existing = self.images
    if len(existing) == 0:
      number = 0
    else:
      number = 1 + max([int(f.basenameNoExt) for f in existing])
    numNew = 0
    numExist = 0
    for image in files:
      if self.imgDir.hasDuplicate(image):
        numExist += 1
      else:
        shutil.copy2(image, self.imgDir + "{0:05d}.jpg".format(number))
        number += 1
        numNew += 1
    if not silent:
      print(green("Added {} new images, {} had already been added.".format(numNew, numExist)))
    return numNew, numExist

  def __repr__(self):
    return self.name

class Keys(object):
  LEFT = 1113937
  RIGHT = 1113939
  ESC = 1048603
  SPACE = 1048608
  ENTER = 1048586
  BACKSPACE = 1113864
  H = 1048680
  Q = 1048689
  R = 1048690
  S = 1048691
  DIGITS = (
    1048624, # 0
    1048625, # 1
    1048626, # 2
    1048627, # 3
    1048628, # 4
    1048629, # 5
    1048630, # 6
    1048631, # 7
    1048632, # 8
    1048633  # 9
  )

class Rect():
  def __init__(self, x=-1, y=-1, w=0, h=0):
    self.x = x
    self.y = y
    self.width = w
    self.height = h
  def fromFile(filename):
    with open(filename, 'r') as f:
      values = [int(line) for line in f.readlines()]
    return Rect(*values)
  def toFile(self, filename):
    with open(filename, 'w') as f:
      for val in [self.x, self.y, self.width, self.height]:
        f.write(str(val) + "\n")
  def __repr__(self):
    return str(dict({'x':self.x, 'y':self.y, 'width':self.width, 'height':self.height}))

class Overlay(object):
  def __init__(self, rect=Rect(), color=(0, 0, 0), width=1):
    self.rect = rect
    self.color = color
    self.width = width

class imageWindow(object):
  def __init__(self, category, title, data, wrap=False):
    self.category = category
    self.title = title
    self.keyCallback = None
    self.helpCallback = None
    self.mouseCallback = None
    self.loadCallback = None
    self.data = data
    self.wrap = wrap
    self.overlays = []

    if not category.exists:
      print(red("Category '{}' does not exist.".format(category)))
      return False

    self.windowName = "imageWindow"
    # http://stackoverflow.com/questions/24842382/fitting-an-image-to-screen-using-imshow-opencv
    cv2.namedWindow(self.windowName, cv2.WINDOW_NORMAL) #WINDOW_NORMAL makes it rescale

    # fullscreen:
    #cv2.setWindowProperty(self.windowName, cv2.WND_PROP_FULLSCREEN, cv2.WINDOW_FULLSCREEN)

  def myMouseCallback(self, event, x, y, flags, param):
    if self.mouseCallback is not None:
      self.overlays, self.data = self.mouseCallback(event, x, y, flags, param, self.data)

  def setMouseCallback(self, mouseCallback):
    cv2.setMouseCallback(self.windowName, self.myMouseCallback);
    self.mouseCallback = mouseCallback

  def setHelpCallback(self, helpCallback):
    self.helpCallback = helpCallback

  def setKeyCallback(self, keyCallback):
    self.keyCallback = keyCallback

  def setLoadCallback(self, loadCallback):
    self.loadCallback = loadCallback

  def loop(self):
    images = self.category.images
    img = None

    load = True
    done = False
    help = True

    i = 0
    key = -1
    while True:
      if help:
        help = False
        if self.helpCallback is not None:
          self.helpCallback(i, self.data)

      if key != -1 and self.keyCallback is not None:
        i2 = i
        i, done, help, load, self.data = self.keyCallback(key, i, self.data)
        if not self.wrap:
          i = max(0, min(len(images)-1, i))
        if i != i2:
          load = True

      if load:
        load = False
        i = i % len(images)
        imgFile = images[i]
        img = cv2.imread(imgFile);
        cv2.setWindowTitle(self.windowName, "{} ({})".format(imgFile.basename, self.title))
        cv2.imshow(self.windowName, img) # show the image

        if self.loadCallback is not None:
          self.overlays, data = self.loadCallback(i, imgFile, self.data)
      if len(self.overlays) > 0:
        imgDrawn = img.copy()
        # draw the overlays
        for overlay in self.overlays:
          box = overlay.rect
          color = overlay.color
          width = overlay.width
          cv2.rectangle(imgDrawn, (box.x,box.y), (box.x+box.width,box.y+box.height), color, width)
        cv2.imshow(self.windowName, imgDrawn) # show the image with the overlay

      # http://stackoverflow.com/questions/35003476/opencv-python-how-to-detect-if-a-window-is-closed/37881722#37881722

      #for j in range(4):
      #  print("{}	{}".format(j, cv2.getWindowProperty(self.windowName, j)))
      #print()
      if cv2.getWindowProperty(self.windowName, 1) != 0:
        done = True

      if done:
        # http://stackoverflow.com/questions/6116564/destroywindow-does-not-close-window-on-mac-using-python-and-opencv
        cv2.destroyWindow(self.windowName)
        for i in range(4):
          cv2.waitKey(1)
        return self.data
      key = cv2.waitKey(10) #wait 10 ms for a key press

def delete(toDelete):
  global categories
  for category in toDelete:
    if category.exists:
      if yesOrNoPrompt("Are you sure you want to permanently delete '{}'?".format(category)):
        category.delete()
        categories = [c for c in categories if c.path != category.path]
        print(yellow("Category '{}' has been deleted.".format(category)))
    else:
      print(red("Category '{}' does not exist.".format(category)))

def info(categories):
  for c in categories:
    print("{}	images: {}".format(c, c.numImages))

def viewKeyCallback(key, i, data):
  if key in (Keys.SPACE, Keys.RIGHT, Keys.ENTER):
    i += 1
  elif key == Keys.LEFT:
    i -= 1
  return i, key in (Keys.ESC, Keys.Q), False, False, data

def viewHelpCallback(i, data):
  print("""	CONTROLS:
Left arrow		move to previous image
Right arrow/Space/Enter	move to next image
ESC			quit
q			quit
window 'x' button	quit
""")

def view(category):
  w = imageWindow(
    category,
    "Viewing {}".format(category),
    []
  )
  w.setKeyCallback(viewKeyCallback)
  w.setHelpCallback(viewHelpCallback)
  w.loop()

def sortKeyCallback(key, i, data):
  inputStr = data['inputStr']
  save = data['save']
  others = data['others']
  numImagesToBeAdded = data['numImagesToBeAdded']
  destinations = data['destinations']
  images = data['images']

  help = False
  done = False

  if key in Keys.DIGITS:
    digit = str(Keys.DIGITS.index(key))
    inputStr += digit
    sys.stdout.write(digit)
    sys.stdout.flush()
  if key == Keys.BACKSPACE:
    if len(inputStr) > 0:
      inputStr = inputStr[:-1]
      sys.stdout.write('\b' * (len(inputStr) + 1) + inputStr + ' ' + '\b')
      sys.stdout.flush()
  if key == Keys.ENTER:
    print()
    valid = None
    try:
      inputInt = int(inputStr)
      valid = inputInt < len(others)
    except ValueError:
      pass
    if not valid and valid is not None:
      print("Number not valid. Enter one of the following: {}".format(tuple(range(len(others)))))
      inputStr = ""
    else:
      if valid is not None:
        prev = destinations[i]
        if prev is not None:
          numImagesToBeAdded[prev] -= 1
        destinations[i] = inputInt
        numImagesToBeAdded[inputInt] += 1
      inputStr = ""
      i += 1
      help = True

  if key == Keys.LEFT:
    i -= 1
    help = True
  elif key in (Keys.RIGHT, Keys.SPACE):
    i += 1
    help = True

  if i >= len(images):
    save = True

  if key == Keys.S:
    save = True
  if save:
    numNew = numExist = 0
    for image, dst in zip(images, destinations):
      if dst is not None:
         a, b = others[dst].add([image], silent=True)
         numNew += a
         numExist += b
    print(green("Added {} new images, {} had already been added.".format(numNew, numExist)))
    done = True

  if key in (Keys.ESC, Keys.Q):
    done = True
  if key == Keys.H:
    help = True

  data['inputStr'] = inputStr
  data['save'] = save
  data['others'] = others
  data['numImagesToBeAdded'] = numImagesToBeAdded
  data['destinations'] = destinations
  data['images'] = images

  return i, done, help, False, data

def sortHelpCallback(i, data):
  others = data['others']
  numImagesToBeAdded = data['numImagesToBeAdded']
  destinations = data['destinations']
  print("""	CONTROLS:
h			show this help text
Left arrow		move to previous image
Right arrow/Space	move to next image
ESC			quit
window 'x' button	quit
0-9, Backspace		input the destination category number
Enter			submit the number and move to the next image
s			sort and quit
""")
  for i2, (c, a) in enumerate(zip(others, numImagesToBeAdded)):
    print("{}	{} ({} images, {} to be added)".format(i2, c, c.numImages, a))

  dst = destinations[i]
  if dst is not None:
    print("This image is in '{}'.".format(others[dst]))

def sort(category, others):
  if len(others) == 0:
    return False
  for other in others:
    if not other.exists:
      print(red("Category '{}' does not exist.".format(other)))
      return False

  if not category.exists:
    print(red("Category '{}' does not exist.".format(category)))
    return False

  images = category.images
  destinations = [None] * len(images)
  numImagesToBeAdded = [0] * len(others)

  data = {
    'inputStr': "",
    'save': False,
    'others': others,
    'numImagesToBeAdded': numImagesToBeAdded,
    'destinations': destinations,
    'images': images
  }

  w = imageWindow(
    category,
    "Sorting {}".format(category),
    data
  )
  w.setKeyCallback(sortKeyCallback)
  w.setHelpCallback(sortHelpCallback)
  data = w.loop()

  if data['save']:
    print("Sorting Successful.")
  else:
    print("Sorting Cancelled.")

# The original image cropper is available at:
# https://sites.google.com/site/learningopencv1/eye-dimensions/image-cropper

def cropKeyCallback(key, i, data):
  mouseDown = data['mouseDown']
  selected = data['selected']
  box = data['box']
  categoryFrom = data['categoryFrom']
  categoryTo = data['categoryTo']
  imgFile = data['imgFile']
  outFile = data['outFile']
  infoFile = data['infoFile']
  numImages = data['numImages']

  help = False
  done = False
  load = False

  if key in (Keys.SPACE, Keys.RIGHT, Keys.LEFT, Keys.S):
    if selected:
      selected = False
      img = cv2.imread(imgFile);
      cropped = img[box.y:box.y+box.height, box.x:box.x+box.width]
      cv2.imwrite(outFile, cropped)
      box.toFile(infoFile)
      print("Cropped '{}'.".format(imgFile.basename))
    if key == Keys.LEFT:
      i -= 1
    elif key in (Keys.SPACE, Keys.RIGHT):
      i += 1
      if i >= numImages:
        done = True

#  if i >= len(images):
#    save = True

  if key in (Keys.ESC, Keys.R):
    load = True

  if key == Keys.Q:
    done = True
  if key == Keys.H:
    help = True


  data['mouseDown'] = mouseDown
  data['selected'] = selected
  data['box'] = box
#  data['categoryFrom'] = categoryFrom
#  data['categoryTo'] = categoryTo
  return i, done, help, load, data

def cropLoadCallback(i, imgFile, data):
  categoryFrom = data['categoryFrom']
  categoryTo = data['categoryTo']
  mouseDown = False
  selected = False
  box = Rect()

  imgBasename = imgFile.basename  
  outFile = categoryTo.imgDir + imgBasename
  infoFile = categoryTo.cropInfoDir + (imgBasename + ".txt")

  if infoFile.exists:
    try:
      box = Rect.fromFile(infoFile)
    except TypeError:
      print(yellow("Removing corrupt data file: '{}'".format(infoFile)))
      infoFile.delete()
#      os.remove(infoFile)

#    img = cv2.imread(inFile);
#    title = "{} ({} -> {})".format(imgName, categoryFrom, categoryTo)
#    cv2.setWindowTitle(windowName, title)
  
  data['imgFile'] = imgFile
  data['outFile'] = outFile
  data['infoFile'] = infoFile
  data['mouseDown'] = mouseDown
  data['selected'] = selected
  data['box'] = box

  overlays = [Overlay(box, (0, 0, 255), 5)]
  return overlays, data

def cropHelpCallback(i, data):
  print("""	CONTROLS:
Left arrow		save and move to previous image
Right arrow/Space	save and move to next image
mouse			draw box to crop image
ESC/r			re-load image to undo changes
s			save
q			quit
window 'x' button	quit
""")

# mouse callback (used to determine area of image to crop)
def cropMouseCallback(event, x, y, flags, param, data):
  mouseDown = data['mouseDown']
  selected = data['selected']
  box = data['box']
  if event == cv2.EVENT_MOUSEMOVE:
    if mouseDown:
      box.width = x-box.x
      box.height = y-box.y
  elif event == cv2.EVENT_LBUTTONDOWN:
    mouseDown = True
    box = Rect(x, y, 0, 0)
  elif event == cv2.EVENT_LBUTTONUP:
    mouseDown = False
    selected = True
    if box.width < 0:
      box.x += box.width
      box.width *= -1
    if box.height < 0:
      box.y += box.height
      box.height *= -1

  data['mouseDown'] = mouseDown
  data['selected'] = selected
  data['box'] = box
  overlays = [Overlay(box, (0, 0, 255), 5)]
  return overlays, data

def crop(categoryFrom, categoryTo):
  if not categoryFrom.exists:
    print(red("Category '{}' does not exist.".format(categoryFrom)))
    return False

  if categoryTo.exists:
    if not yesOrNoPrompt("Category '{}' exists. Continue?".format(categoryTo)):
      return False

  categoryTo.create()

  images = categoryFrom.images

  data = {
    'mouseDown': False,
    'selected': False,
    'box': Rect(),
    'categoryFrom': categoryFrom,
    'categoryTo': categoryTo,
    'numImages': categoryFrom.numImages
  }

  w = imageWindow(
    categoryFrom,
    "Cropping {} -> {}".format(categoryFrom, categoryTo),
    data
  )

  w.setKeyCallback(cropKeyCallback)
  w.setHelpCallback(cropHelpCallback)
  w.setLoadCallback(cropLoadCallback);
  w.setMouseCallback(cropMouseCallback);

  w.loop()
  return True

#based on:
# https://github.com/mrnugget/opencv-haar-classifier-training/blob/master/bin/createsamples.pl
def createsamples(positive, negative, outputdir, width, height, totalnum=7000):
  cmd = "opencv_createsamples" + \
    " -bgcolor 0" + \
    " -bgthresh 0" + \
    " -maxxangle 1.1" + \
    " -maxyangle 1.1" + \
    " -maxzangle 0.5" + \
    " -maxidev 40" + \
    " -w " + str(width) + \
    " -h " + str(height)

  outputdir.createDir()
  tmpfile  = 'tmp' + str(random.randint(100000,999999))
  with open(positive, 'r') as f:
    positives = f.read().splitlines()

  with open(negative, 'r') as f:
    negatives = f.read().splitlines()

  # number of generated images from one image so that total will be totalnum
  numfloor = int(totalnum / len(positives))
  numremain = totalnum - numfloor * len(positives)

  progressBar = ProgressBar(len(positives))
  for k in range(len(positives)):
    img = positives[k]
    num = numfloor
    if k < numremain:
      num += 1

    # Pick up negative images randomly
    localnegatives = []
    for i in range(num):
      localnegatives.append(random.choice(negatives))
    with open(tmpfile, 'w') as f:
      f.write('\n'.join(localnegatives))

    vec = outputdir + (os.path.basename(img) + ".vec")
    command = "{} -img {} -bg {} -vec {} -num {}".format(cmd, img, tmpfile, vec, num)
    os.system(command + " > /dev/null")
    progressBar.tick()
  os.remove(tmpfile)

def train(category, numStages, width, height, negativeCategories):
  if not category.exists:
    print(red("Category '{}' does not exist.".format(category)))
    return False

  positivesFileName = category.trainingDir + "positives.txt"
  numPositives = 0
  with open(positivesFileName, 'w') as positivesFile:
    for image in category.imgDir.ls:
      positivesFile.write(str(image) + "\n")
      numPositives += 1

  negatives = []
  for category2 in negativeCategories:
    if not category2.imgDir.exists:
      print(yellow("Directory '{}' does not exist.".format(category2.imgDir)))
    else:
      for image in category2.imgDir.ls:
        negatives.append(image)

  numNegatives = len(negatives)
  if numNegatives == 0:
    print(red("No negatives were found."))
    return False

  negativesFileName = category.trainingDir + "negatives.txt"
  with open(negativesFileName, 'w') as negativesFile:
    for image in negatives:
      negativesFile.write(str(image) + "\n")

  dataDir = category.resultsDir + "{0:05d}".format(numStages)
  if dataDir.exists:
    print(red("Directory '{}' already exists.".format(dataDir)))
    return False

  existing = category.resultsDir.ls
  if len(existing) > 0:
    numbers = [int(f.basenameNoExt) for f in existing]
    #numbers = [n for n in numbers if n < numStages]
    if len(numbers) > 0:
      highest = max(numbers)
      highestName = category.resultsDir + "{0:05d}".format(highest)
      shutil.copytree(str(highestName), str(dataDir))
      paramsFile = dataDir + "params.xml"
      if paramsFile.isFile:
        with open(paramsFile, 'r') as f:
          doc = xmltodict.parse(f.read())
        width = int(doc['opencv_storage']['params']['width'])
        height = int(doc['opencv_storage']['params']['height'])
        print("Using width and height from previous run: {}x{}".format(width, height))

  positivesDir = category.trainingDir + "positives"
  positivesVec = category.trainingDir + "positives.vec"

  numPositives *= 50
  positivesReduced = int(numPositives * 2 / 3)
  negativesReduced = int(numNegatives * 2 / 3)

  if not positivesVec.exists:
    print(green("Creating {} samples...".format(numPositives)))

    createsamples(positivesFileName, negativesFileName, positivesDir, width, height, numPositives)

    print(green("Merging .vec files..."))
    os.system("mergevec" + \
      " -v " + str(positivesDir) + \
      " -o " + str(positivesVec)
    )

  dataDir.createDir()

  print(green("Training the classifier (this will take multiple days)..."))
  os.system("opencv_traincascade" + \
    " -data " + str(dataDir) + \
    " -vec " + str(positivesVec) + \
    " -bg " + str(negativesFileName) + \
    " -numStages " + str(numStages) + \
    " -minHitRate 0.999" + \
    " -maxFalseAlarmRate 0.5" + \
    " -numPos " + str(positivesReduced) + \
    " -numNeg " + str(negativesReduced) + \
    " -mode ALL" + \
    " -precalcValBufSize 1024" + \
    " -precalcIdxBufSize 1024" + \
    " -w " + str(width) + \
    " -h " + str(height)
  )

def help():
  print("""USAGES:
cascademan help
	display this text

cascademan set root <directory>
	set the root directory where all the categories of images will be stored

cascademan settings
	list the settings

cascademan create <category1> <category2>  ...
	create one or more categories

cascademan add <category> <file/dir> <file/dir> ...
	add images to a category, searching recusively for images

cascademan list/ls
	list the categories

cascademan delete/remove <category>
	delete a category

cascademan rename/move <category> <newCategory>
	renames the category to newCategory

cascademan copy <category> <newCategory>
	copies the category to newCategory

cascaedman info [category1] [category2] ...
	show info about each category
	If no arguments are given, show info about all categories

cascaedman view <category>
	view the images from a category in a slideshow

cascademan sort <category> [category1] [category2] ...
	sort category into the other categories
	if the other categories are omitted, use all categories

cascademan crop <categoryFrom> [categoryTo]
	crop images from categoryFrom into categoryTo
	if categoryTo is omitted, <categoryFrom>_crop is used

cascademan train <category> <numStages> <width> <height> <negativeCategory1> <negativeCategory2> ...
""")

def reloadRoot():
  global categoriesDir, categories
  if 'root' in globalCfg:
    categoriesDir = Path(globalCfg['root']) + "categories" # types of images
    categoriesDir.createDir()
    categories = Category.all(categoriesDir)
  else:
    print(yellow("Root directory not set. Set it with 'cascademan set root <directory>'."))
    categoriesDir = None
    categories = []

def findCategory(categories, name):
  for category in categories:
    if category.name == name:
      return category
  print(yellow("Category '{}' does not exist.".format(name)))
  return None

def findCategories(categories, names):
  found = []
  for name in names:
    category = findCategory(categories, name)
    if category is not None:
      found.append(category)
  return found

def parseCommand(command, args):
  global categoriesDir, categories
  if command == "help":
    help()
  elif command == "set":
    key = args[0]
    value = args[1]
    if key == 'root':
      value = os.path.abspath(value)
    globalCfg[key] = value
    globalCfg.dump()
    if key == 'root':
      reloadRoot()
  elif command == "settings":
    print(globalCfg)
  elif command == "create":
    if len(args) > 0:
      for categoryName in args:
        category = findCategory(categories, categoryName)
        if category is None:
          category = Category(categoriesDir, categoryName)
          category.create()
          categories.append(category)
          print(green("Added category '{}'.".format(category)))
        else:
          print(yellow("Category '{}' already exists.".format(category)))
    else:
      print(red("Not enough arguments for create function."))
  elif command == "add":
    categoryName = args[0]
    sources = args[1:]
    category = findCategory(categories, categoryName)
    if category is None:
#      if len(sources) == 0:
#        sources.append(categoryName)
#        categoryName = os.path.basename(categoryName)
#        category = findCategory(categories, categoryName)
      category = Category(categoriesDir, categoryName)
      category.create()
      categories.append(category)
    category.add(sources)
  elif command == "list" or command == "ls":
    for category in categories:
      print(category)
  elif command == "remove" or command == "delete":
    if len(args) > 0:
      delete(findCategories(categories, args))
    else:
      print(red("Not enough arguments for delete function."))
  elif command == "rename" or command == "move":
    category = findCategory(categories, args[0])
    newName = args[1]
    category.rename(newName)
  elif command == "copy":
    category = findCategory(categories, args[0])
    newName = args[1]
    newCategory = category.copy(newName)
    if newCategory is not None:
      categories.append(newCategory)
  elif command == "info":
    if len(args) > 0:
      info(findCategories(categories, args))
    else:
      info(categories)
  elif command == "view":
    category = findCategory(categories, args[0])
    view(category)
  elif command == "sort":
    category = findCategory(categories, args[0])
    if len(args) > 1:
      sortTo = findCategories(categories, args)
    else:
      sortTo = categories
    sortTo = [c for c in sortTo if c != category]
    sort(category, sortTo)
  elif command == "crop":
    categoryFrom = findCategory(categories, args[0])
    if len(args) > 1:
      categoryToName = args[1]
    else:
      categoryToName = str(categoryFrom) + "_crop"
    categoryTo = Category(categoriesDir, categoryToName)
    #categoryTo = findCategory(categories, categoryToName)
    #if categoryTo is None:
      
#      categoryTo.create()
    categories.append(categoryTo)
    crop(categoryFrom, categoryTo)
  elif command == "train":
    category = findCategory(categories, args[0])
    numStages = int(args[1])
    width = int(args[2])
    height = int(args[3])
    negativeCategories = findCategories(categories, args[4:])
    train(category, numStages, width, height, negativeCategories)

def completer(text, state):
  options = [x for x in commands if x.startswith(text)]
  try:
    return options[state]
  except IndexError:
    return None

def console():
  readline.set_completer(completer)
  readline.parse_and_bind("tab: complete")

  print(blue(bold('Type help for the list of commands.')))
  prompt = bold(cyan('~ '))
  lastCommand = ''
  try:
    while True:
      #TODO fix buggy input field
      command = input(prompt)
      if command.strip() == '':
        command = lastCommand
      try:
        l = command.split(' ')
        parseCommand(l[0], l[1:])
      except IOError as e:
#      except IOException as e:
        print(uline(Dred(e)))
      lastCommand = command
  except (KeyboardInterrupt, EOFError) as e:
    print('')


#stores the root dir
globalCfg = CfgFile(os.path.join(os.path.expanduser("~"), ".cascademan"))
reloadRoot()

if len(sys.argv) < 2:
  console()
else:
  command = sys.argv[1]
  args = sys.argv[2:]
  parseCommand(command, args)







