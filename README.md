# FootballTracker
Ball detector and tracker for videos of football games

## Introduction 
Football is with 250 million players in over 200 countries by far the most popular sport in the
world. The international competitions such as the World Cup and the Champions League, as
well as the individual country championships are watched by billions of people around the world.
In recent years, the analysis and statistics of football matches have become
more and more in demand. Coaches need the statistics of the opposing team to develop new
strategies and bettors increasingly ask for the probabilities of the match results to know which
team to bet on. Analysts use positional information to help explain tactics. 

Many analyzes can be performed at a high level using computer vision. Automating the
tracking and recognition of objects such as the players and the ball on football videos makes it
easier to analyze games and identify violations during a match. Computer vision can help with
this for many applications such as shot classification, ball possession statistics, or event detection. For all these applications, the ball position is needed as a crucial piece of information. 

However, the detection of the ball in a video is not trivial. Occlusions, shadows, the presence of
very similar objects near the pitch lines and in the players’ body regions, changes in appearance
e.g. when the ball is in the goal and is obscured by the net and unpredictable movement due
to players’ shots mislead the most advanced algorithms. Furthermore, the ball is very small
compared to other objects visible in the observer scene and its size varies significantly depending
on its position. 

In this work a ball detection method that uses Convolutional Neural Networks and computer
vision techniques is presented. The method is evaluated using the ISSIA-CNR Soccer dataset
that contains six videos of synchronized long shot views of the football pitch acquired by six
cameras recording at 25 frames per second. The videos are acquired during matches of the
Italian ’Serie A’. 

## Concept of the Deep Learning Model

The method that is applied in this work to track the ball uses the sum of the aggregated image
frames of each video and the given annotations with the ball position of each frame to learn
a model that can predict where the ball is in a given image. After the learning progress, the
model can predict the ball position for each individual frame and by merging the frames and
the predictions a video can be created again, estimating the position of the ball at each point
in time and therefore tracking the ball.

It has turned out that in addition to the original images, it is also beneficial to use images
processed by computer vision and image processing techniques for the training, therefore dilated and thresholeded images are used additionaly. 

![cv](https://user-images.githubusercontent.com/63397065/153750136-c5b73327-6ae6-4ab2-9aaf-ee37cb824a99.png)

Another piece of additional helpful information for the training of the model is a vector
with the last ten positions of the ball. Therefore, the inputs for the model are the original
image frames, the frames that were preprocessed with computer vision and image processing
techniques and a handcrafted feature vector that contains the last ten positions of the ball.

The output to be predicted by the model is the ball position. An analysis of different deep
learning network structures showed that modeling the task as a regression problem does not
provide sufficiently accurate results. In the regression problem, the network directly predicts
the x-coordinate and the y-coordinate of the ball as a real value. Better results are obtained
when the network is designed as a classification problem that estimates the position of the ball
in one of the discrete sections of a grid superimposed on the image.

![gitter](https://user-images.githubusercontent.com/63397065/153750176-9373732c-5bb3-4397-9a9f-eb48fbe346ef.PNG)

An analysis showed that a choice of 45 columns and 25 rows leads to good results. If the ball is not present in an image frame, the
desired output is 0.

The DL model that is used in this work consists of two sequential CNN for both the original
image and the processed image. The outputs of the two CNN as well as the features that
represent the last ten positions of the ball are then merged together to a flattened vector that
contains the information of all three inputs. This flattened vector is then used as the input
vector of a fully connected ANN that predicts the ball position in one of the sections of the
image grid.

![cnn](https://user-images.githubusercontent.com/63397065/153750187-d2ec065e-4071-4268-a984-50045080c33d.PNG)

Both sequential CNN use the same structure. This DL network architecture results in 36,839,006 trainable
parameters.

![network](https://user-images.githubusercontent.com/63397065/153750192-e6e1a551-3055-4ff6-ab02-58d58e2b43c5.PNG)

## Evaluation

For approx. 96% of all images the model predicts the correct grid section for the ball
location, which is a high amount considering the 1126 different classes that represent the grid
sections. The MAE results to 14.60 pixels, which is also relatively small, since a picture is
1920x1088 pixels.

![acc](https://user-images.githubusercontent.com/63397065/153750285-16ca4db5-c8d6-4bad-bdff-edefa8d17801.PNG)

