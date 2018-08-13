# XX Place Solution; 0.XXXX private LB 

## Third Kaggle competition and a FIRST/SECOND X medal!  

Hi, I’m a 15 year old Kaggle beginner and this is my third Kaggle competition and FIRST/SECOND X medal! This competition was different from others since it wasn’t a straightforward supervised learning problem which made it challenging, and I wouldn't have achieved this score without all the ideas shared on the discussion forum. 

For most the first two months of the competition, I mostly focused on deep learning based solutions but found them hard to train and they took a long time. It was only in the last month of the competition that I focused on implementing an unsupervised clustering based solution.

Some of my deep learning based ideas that I partially completed or didn’t attempt included: Using an mlp to find triplets, implementing a PointNet to cluster tracks, and creating word2vec style embeddings for each track and then clustering the embeddings. Unfortunately, I wasn’t able to get any of these approaches to work really well in the 2 months. After that, I shifted my focus to clustering based approaches.

My final solution was a DBSCAN clustering and helix unrolling with z shifting and track extension.

### Features

The features I ended up using were:

`cos(a), sin(a), z/rt, z/r, x/r, y/r` 

where 

`a = arctan2(y, x) - arccos(mm * ii * rt)`

`r = sqrt(x^2 + y^2 + z^2)`

`rt = sqrt(x^2 + y^2)`

These features were from [Grzegorz's] [1] and [Kha A Vo's] [2] kernels. Unfortunately, I didn’t have the time to find better features with the ideas shared in [this] [3] discussion post. I originally used `cos a, sin a, z/r, and z/rt` as my features; When merging based on track length, these features give you a score of 0.35 with no weights, and I couldn’t take it beyond 0.5 even after extensive Bayesian optimization. The addition of `x/r and y/r` and the default weights from the second kernel gives me a score of 0.54 without any z-shifting or track extension.

### Merging

My merging strategy is very simple since I didn’t have time to improve it. I simply assign hits to the longest track with no more than 25 hits. 

### Z-shifting

I use 11 z-shifts uniformly distributed around the origin. I search +/- 5mm with steps of 1mm and the origin itself for a total of 11 z-shifts. The drawback of using so many z-shifts is that my running time increases by a factor of 11.

### Track Extension

I use a modified version of track extension from [Heng's track extension post] [4] . I found that for me, the optimum number of track extension rounds is 6. I wasn't able to use more because each further track extension gave diminishing increases in score and took an extra 1-2 minutes.

### Hyperparameters

I used Bayesian optimization on top of the weights given in the second kernel. Also optimizing the number of DBSCAN iterations and it's epsilon hyperparameter further increased the score. My final hyperparameters are:

    cos(aa) and sin(aa): 1.7
    z/rt: 0.8
    z/r: 0.2
    x/r: 0.015
    y/r: 0.015

### Compute and Parallel Processing

For this competition, I used a preemptible 96 core 86 Gb RAM virtual machine from GCP. All together, I'm running 300 iterations * 11 z-shifts * 2 directions = 6600 iterations of DBSCAN and 6 rounds of track extension. Each event takes about 9-10 min when I use Python's multiprocessing.Pool to parallelize the iterations; After reading [cpmp's post] [5] , I see that most people parallelize over the events of the test set to reduce overhead.

### Things I Didn't Do

Look for better features; I didn't have the math expertise of the higher ranking competitors and didn't have the time at the end of the competition when the " Criteria for Good Features" discussion post was posted.

Develop more sophisticated track merging; Focusing more on merging than z-shifting at first could have let me get a higher score out of my earlier features.

Optimizing more hyperparameters; I didn't optimize the helix unrolling or track extension hyperparameters.

My code is available at [this] [6] GitHub repository

[1]: https://www.kaggle.com/sionek/mod-dbscan-x-100-parallel
[2]: https://www.kaggle.com/khahuras/0-53x-clustering-using-hough-features-basic
[3]: https://www.kaggle.com/c/trackml-particle-identification/discussion/61590
[4]: https://www.kaggle.com/c/trackml-particle-identification/discussion/58194
[5]: https://www.kaggle.com/c/trackml-particle-identification/discussion/62883
[6]: https://github.com/bkahn-github/TrackML
