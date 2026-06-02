# Bark  


# Links  

* [On GitHub](https://github.com/suno-ai/bark.git)  
* [Voices](https://suno-ai.notion.site/8b8e8749ed514b0cbf3f699013548683?v=bc67cff786b04b50b3ceb756fd05f68c)  

# Install  

> This uses [Anaconda / Conda](learn_to_code/python/conda); you can simply use a Python virtual environment if you wish, just swap out the `conda` commands for your equivalent virtual environment commands.  

!> If there are specific Python versions, library versions, etc, this is because as of the writing of this (August 2025) this is what specifically worked; it will probably change in the future.  


To install the <font color="green">Bark</font> library in Python: 

1\. Delete the bark conda environment, if it exists: `conda env remove --name bark`  

2\. Create the Conda environment: `conda create --name bark python=3.12.11 numpy scipy`  
  * `scipy` - For audio processing  

3\. Activate the Conda environment: `conda activate bark`  

4\. Install [torch](learn_to_code/python/machine_learning/pytorch?id=installing)  
  * You will need to install `torch torchvision torchaudio`  

5\. Run PIP install: `pip install git+https://github.com/suno-ai/bark.git`  


# Bark Abandoned  

I abandoned Bark - Why? Well, firstly, the voices all had a hint of static; the English voices were....OK, but there was only ONE female English voice - and the other 8 male English voices were 'OK' at best. There were 130 voices overall, but....120 were foreign, and half the time, the vice changed MID PROCESS, sometimes even to the OPPOSE GENDER. It, quite frequently, had background music constantly, hallucinated words, sometimes the foreign languages would just start speaking in their native language instead of English, etc. Due to the unpredictability - and the fact that I read Sumo is no longer maintaining it - I tapped out.  


