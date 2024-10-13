# Stable Diffusion  

# Discord  

* [Stable Diffusion](https://discord.gg/stablediffusion)  
* [Easy Diffusion](https://discord.com/invite/u9yhsFmEkB)  

# Sources  

* [Unstable Diffusion](https://www.unstability.ai/) - Online version.  
* [Reddit - Stable Diffusion Guide](https://www.reddit.com/r/StableDiffusion/wiki/guide/)  
   * [Easy Diffusion](https://github.com/easydiffusion/easydiffusion) - easy local SD instance for Linux. 
      * [How to Use Guide](https://github.com/easydiffusion/easydiffusion/wiki/How-to-Use)
* [Civitai - Stable Diffusion Models](https://civitai.com/) and also discussion.   
* [Arki's Stable Diffusion Guides](https://stablediffusionguides.carrd.co/)  
   * [Here](https://web.archive.org/web/20230619050647/https://stablediffusionguides.carrd.co/)  is the wayback version, as the original seems to be down.  
* [Stable Diffusion Discord Guide](https://rentry.org/UnofficialUnstableGuide)
* [2 EASY Stable Diffusion Tricks for Fantastic Results!!!](https://www.youtube.com/watch?v=JNRejp53JBU)  
* [DreamBooth / LyCORIS / LORA Guide](https://civitai.com/models/45539?modelVersionId=50157)  
* Prompt Help  
   * [Reddit - What are some prompt "tricks" that you've found?](https://www.reddit.com/r/StableDiffusion/comments/y07yo6/what_are_some_prompt_tricks_that_youve_found/)  
   * [Yandex](https://yandex.com/), as you can use img2img to 'help' the algorithm and Yandex is good at using natural language to find a base image.  
* [Slider Tool](https://github.com/rohitgandikota/sliders)  

# Tutorials  

* ControlNet
   * [NEW ControlNet 1.1! ControlNet Tutorial](https://www.youtube.com/watch?v=WZg3e6B2yPQ)  
   * [WOW! ControlNet 1.1 Tiles Tutorial - High Resolution for Everyone!](https://www.youtube.com/watch?v=EmA0RwWv-os&t=50s)  
* Training LoRAs  
  * [How To Train Own Stable Diffusion LoRA Models](https://techtactician.com/how-to-train-stable-diffusion-lora-models/)  
  * [Make your own Loras, easy and free](https://civitai.com/models/22530/guide-make-your-own-loras-easy-and-free) (Uses an online service)  
  * [Lora Training for Beginners](https://huggingface.co/hollowstrawberry/stable-diffusion-guide/blob/main/README.md#train) (uses Kohya GUI)  
  * [First Ever SDXL Training With Kohya LoRA - Stable Diffusion XL Training Will Replace Older Models](https://www.youtube.com/watch?v=AY6DMBCIZ3A)  
  * [EDG's Tutorials](https://ko-fi.com/post/EDGs-tutorials-P5P6KT5MT)  
     * [LoRA](https://www.canva.com/design/DAFcn1l_ulE/view)  
     * [LoHA](https://www.canva.com/design/DAFeAteHW18/view)  
     * [LoCon](https://www.canva.com/design/DAFiQbzgpqQ/view)  
* General Stable Diffusion guide - a good start by [hollowstrawberry](https://huggingface.co/hollowstrawberry/stable-diffusion-guide)  
* [After Detailer (ADetailer) Tutorial](https://stable-diffusion-art.com/adetailer/#Installing_After_Detailer_extension)  
* [DreamBooth / LyCORIS / LORA Guide](https://civitai.com/articles/7/dreambooth-lycoris-lora-guide), by malcolmrey, a prominent Civitai modeler  
* [Zovya's Ultra Sharp High Contrast Tutorial +vae&upscaler](https://civitai.com/models/34192/ultra-sharp-high-contrast-tutorial-vaeandupscaler)
* [Making a Lora is like baking a cake.](https://civitai.com/articles/138/making-a-lora-is-like-baking-a-cake)
* [Characters, Clothing, Poses, Among Other Things: A Guide](https://civitai.com/articles/680/characters-clothing-poses-among-other-things-a-guide)  
* [Using SD 1.5 Checkpoints as Refiners and Detailers to Enhance Realism](https://civitai.com/articles/2287)  
* [Guy's Generation Toolbelt](https://civitai.com/articles/759)  
* [Second Dinner's Guide to Fast, Easy, Free, High Quality LoRAs](https://civitai.com/articles/5758)

# Locations of Models / LoRAs  

* [Huggingface](https://huggingface.co)  
   * [Here](https://huggingface.co/spaces/huggingface-projects/diffusers-gallery) is the space specifically for Diffusers, although there is no search...   
* [Civitai](https://civitai.com/)  
   * [Listing of Shows and Common Groups](https://civitai.com/articles/713)   
* [TensorArt](https://tensor.art/)  
* [PromptHero](https://prompthero.com/)  
* [Mage.space](https://www.mage.space/)  

# Other Tools  

* [waifuc](https://github.com/deepghs/waifuc), a dataset collection and processing tool  
* [waifuset](https://github.com/Eugeoter/waifuset), a user-friendly dataset processing UI  
* [CLIP Interrogator](https://github.com/pharmapsychotic/clip-interrogator-ext)  
* [Dataset Tag Editor](https://github.com/toshiaki1729/stable-diffusion-webui-dataset-tag-editor)  

# Inspecting Image Tags  

Many images generated with a flavor of Stable Diffusion can - and usually do - embed metadata into your generated images; this metadata usually contains your prompt, the model used, etc. This section will list a few tools you can use to inspect / modify this metadata.  

> Different tools that use Stable Diffusion may put the metadata in different spots; for example:
> * Automatic1111 uses the `Parameters` tag   


## exiftool  

The `exiftool` command is probably one of the more powerful commands that can be used. To install, become root and then run: 
```
apt install libimage-exiftool-perl  
```  

A basic way to see the metadata is to just use the command and the filename:  
```
exiftool YourImageFile.png  
```  
* This displays most pertinent tags on the image.  


You can view specific tags too. There are hundereds of parameters for `exiftool`, but a basic way to view tags is like so:  
```
exiftool -Parameters YourImageFile.png  
```  
* `-Parameters` is the image tag used by Automatic1111   
   * This can take any other tag in the data - for example, `-ImageSize` or `-ExposureTime`  


If you wish to remove all extra metadata (i.e. `Parameters`), you can use the `-all=` to remove everything like so:  
```
exiftool -all= YourImageFile.jpg
```   

Its possible to use `exiftool` to write tags as well; the format is:
```
exiftool -Comment="some text here" YourImageFile.png  
```  
* The tag _must_ be defined.  
* `-Comment` is the tag name - you can edit most - but not all - of them  
   * Sadly you cannot modify `-Parameters` - you can only delete the contents (see above).  


## imagemagick

imagemagick is another way to view metadata; I like this one as it has a nice `-verbose` option.  

To install, become root and then run: 
```
apt install imagemagick
```  

A basic way to see all of the metadata:  
```
identify -verbose YourFilenameHere.png
```  
* This displays most pertinent tags on the image.  


# Models  


   
# Loras  
   * [Tim Burton Animation Style](https://civitai.com/models/94747/tim-burton-animation-style)  
      * Tags: TIMBURTON ANIMATION  
   * [Rick and Morty Characters and Style](https://civitai.com/models/43318/rick-and-morty-characters-and-style-lora)  
      * Tags: RM_STYLE, BETH_SMITH, MORTY_SMITH, SUMMER_SMITH, RICK_SANCHEZ, RM_JESSICA  
   * [More Details](https://civitai.com/models/82098/add-more-details-detail-enhancer-tweaker-lora)  
      * Author:  
         * between 0.5 and 1 weight  
         * interesting results at negative weight, sometimes  


# Prompts  

## Good Positive Prompts  

* `upper body` to show upper body.  

## Good Negative Prompts

* `(worst quality, low quality, normal quality:1.4)`  
* `watermark`  
* `logo`  
* `skin blemish`  
* `gaps in teeth`  
* `malformed limbs`  


## Negative Prompts Need Testing  
* `bad anatomy`  
* `bad hands`   
* `(cropped)`  
* `oversaturated`  

# LCM  

> I am just providing quick notes - I got this from [civitai](https://civitai.com/articles/3297).  

LCM is a new method that cuts down on iterations to generate SD 1.5 images (so they are made _much_ faster). There are checkpoints and LoRAs that are made that are specifically tailored to LCM; generally speaking, you need the LCM sampler, but otherwise:  
* You can use LCM Checkpoints on other samplers (with varying levels of success).  
* Its said you cannot use LCM sampler on a non LCM checkpoint  
   * That said, I have done it; it can work, but often you get artifacts or items that are nonsensical.  
* If you use Automatic1111, you can install the [AnimateDiff Extension](https://github.com/continue-revolution/sd-webui-animatediff) (also in the default listing of Automatic1111 extensions, so you do not have to go to github directly).  
* Just make SURE to set CFG to 1-2 for the associated checkpoints - any higher you will have over-burned image issues.  
   * Coincidentally - if you switch back to another checkpoint, make _sure_ to change the sampler **and** the CFG score back to 6-7ish.  


# Unfiled notes  

* SDXL is more literal and detail oriented in it's prompting so you really have to describe the style or aesthetic you want as it since it's much more controllable  (EasyDiffusion Discord)  
* Many sdxl models don't like going below 1024x1024 resolution. (EasyDiffusion Discord)  
* Do NOT use a SD 1.5 VAE on SDXL. (EasyDiffusion Discord)  
* Murky - Sd 1.5 trained on resolution 512. Going too high from original base usually creates multiple people or parts  
* Madrang (EasyDiffusion) - For a basic overview of SD go [here](https://jalammar.github.io/illustrated-stable-diffusion/) and then for a follow-up, [here](https://stable-diffusion-art.com/how-stable-diffusion-work/)  
* The base for most Anime models is NAI (Novel AI), but its hard / impossible to find. It MAY be [here on huggingface](https://discord.com/channels/1037619093387350066/1037622825617461258/1170860074919542884) but I am not sure if thats it.  
* Sometimes, you can use _multiple LoRAS of the same subject concurrently_ for better results. For example, if you wanted a pciture of Mila Kunis at the Oscars and the LyCORIS / LORA is not working well, you can use _two_ LoRAS/LyCORIS (or one of each, as apparently that is preferable) and the effect can actually be stronger. 
   * Remember, though, if you do this you need to lower the weight of _both_.  
   * I dont know how this works if the tags are different - I am sure you can get it to work, but you may need to play with it.  
   * This was first reported by [malcolmrey on civitai](https://civitai.com/articles/1721)  
* Adetailer location: https://github.com/Bing-su/adetailer.git  
* Discussion with 'TheGooder' on [civitai](https://civitai.com/models/76851/betty-rubble-the-flintstones) re: training loras

```
I'm not very knowledgeable about all of this:

I'm using collab, so you want this file?

training_config.toml

If yes, I can share it.


dextermorgan
6 months ago
@TheGooder yes, please :)


TheGooder
OP
6 months ago
@dextermorgan 

https://drive.google.com/file/d/1-0O59IDVen8zxVl8t3uV2LeXtraPv3ng/view?usp=sharing


dextermorgan
6 months ago
@TheGooder Thanks! Did you follow some guide or learned how to do this by yourself?
And could you share how many steps per dataset image you're using? Like what your dataset image folder named like, e.g. "20_betty rubble" 😅


TheGooder
OP
6 months ago
@dextermorgan 

No, I didn't follow any guide but I can recommend this one for collab:

https://civitai.com/models/22530/guide-make-your-own-loras-easy-and-free

[Could you share how many steps per dataset image you're using?]

note: your images multiplied by their repeats is between 200 and 400

Well if you take this Lora for example,

I had 40 images, so i used 10 epoch(2000 steps), if I Had used 50 images it would be 8 epoch(1600 steps)

also, I use 10 epoch for any dataset with 40 and less images

Also, this is what a text file looks like:

Betty Rubble, 1girl, solo, smile, short hair, simple background, black hair, white background, dress, bow, bare shoulders, standing, full body, barefoot, black eyes, flat chest, makeup, blue dress, short dress, lipstick, hands on hips

here the link for the dataset_config:

https://drive.google.com/file/d/1--2tHFcK9dNNvZWcbII7ntHnmlQY6HPT/view?usp=sharing
```
