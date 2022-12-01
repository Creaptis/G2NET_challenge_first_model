The G2net challenge (https://www.kaggle.com/competitions/g2net-detecting-continuous-gravitational-waves) consists in finding continuous gravitational waves in highly noisy signal.

One characteristic of the signal we seek is that it is very consistant through time. However the excessive noise in the data makes finding the signal through a local analysis challenging.
One key idea for solving this challenge is thus to find an optimal way to make a 'global' analysis of the data as opposed to a 'local' one.


Here is the chain of reasoning that lead me to my current network :
- as explained in [1], training a classification network on data with and without signal is wasteful, indeed data without signal is constituted of noise only and the patern of noise cannot pratically be learned by a NN. A more intelligent approach is to train a network to infer the parameters of a signal on data with signal only and then to transfer learn the weights of this network to a classification network that can be fine-tuned 
- this transfer learning to a classification network is not even really necessary in our case, once the 'parametre estimation' network gives us its result we can check for the presence of a signal trhough filtering [2]
- However the network presented in [1] will only work for reasonable 'Noise to signal ratio' and for short period of time. They network is based on convolutions and is thus very 'local' early on in the network. Indeed one intuition for why this won't work in our case is that our high noise ratio will generally randomly generate 'patterns' which will mislead the network in the early layers. The only way to overcome this problem is to make information travel throughout the 'time' axis very early on in the network. One other possibility would be to use somme sort of residual neural network but I felt less confident with the intuition of the reason why it would work. Very Deep Residual Neural network have been shown to behave like an ensemble of network [4] and an ensemble might not suffice to overcome our noise problem. (this is no demonstration that residual nets would not be effective, just an explanation of why I chose an other path)
- I thus concluded that some sort of 'sideway' convolution might suffice to convey information throughout the 'time' axis. The closest idea explored in the litterature is in [5] but it differs a lot from what I envision .It seemed however extremly computationaly expensive and should thus be implemented with care of optimizing as much as possible (by generating 'groups' of what I call "weaving" convolutions : meaning convolutions that take one out of every n modality of a feature map)
- I chose the task of segmentation over that of parameter estimation as I thought that segmentation allows for a more rich update at every step of the learning process. Indeed segmentation might reward the network for recognising a part of the signal and not necessarily all of it. parameter estimation requires no mistake been made and gives information on less dimensions (3 dimensions). Successful segmentation will require the network to learn at least as many latent parameters than the real number of parameters the "parameters estimation network" will infer. But allowing the network to express these parameters with more leeway in a hidden way gives hope that it will need less layers and might learn faster. 
- segmentation with some mask on the signal will force the network to learn the behaviour of the global signal.
- I will first do a proof of concept network here with little noise but some temporal masking. thes size of the input data will also be reduced for faster training
- because each time step mesures 30 minutes, our 'horizontal convolution' can exploit this by being considered as a convolution on data[:, i::48] where i belongs to [0,48). This exploits the fact that our signal is invariant to passing days ( 30 minutes * 48 time steps = 1 day) We thus obtain a fairly light sideway convolution. 

[1]
[2]
[3]
[4]
[5] High-efficiency chaotic time series prediciton based on time convolutional neural network





