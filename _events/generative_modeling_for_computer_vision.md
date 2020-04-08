---
title: "Generative Modeling for Computer Vision"
date: 2020/01/26

tagline: "Applied Machine learning days 2020 workshop"
header:
    overlay_image: /images/events/vae_add_smile.png
    teaser: /images/events/vae_latent_space.png
---
## Overview
That workshop was held on [Applied Machine Learning Days 2020](https://appliedmldays.org/) in Switzerland, Lausanne.
[Sandro](https://www.linkedin.com/in/sandrobarna) and me were organizers of that workshop. 

It was about **Generative Modeling for Computer vision**. Concretely, we used several types of AutoEncoders 
(variational, vector-quentized, etc) to generate images of handwritten digits (MNIST) 
and celebrity faces (celebA dataset). Most exciting part here is the latent space of such models. Variational AutoEncoder 
has very continuous latent space and you can simply add vectors of some interpretable features (smile, sunglasses) to human 
vector in latent space, then decode resulting vector and you will get smiling person or person with sunglasses.
You can also see examples of latent space visualization in 2D using 2 different interpretable features. It shows very
smooth transition between each generated image.

## Resources
[AMLD 2020 Workshop](https://appliedmldays.org/workshops/generative-modeling-for-computer-vision?fbclid=IwAR1cT39lJwipvxbaLZQz7ynFcSq112k00aKxNn_aedZFmbXVWcjaxpwgQXg)

[Repository containing presentation file and workshop materials](https://github.com/MaxinAI/amld2020-workshop)