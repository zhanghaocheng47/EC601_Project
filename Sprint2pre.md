#QUESTIONS

Definition of architecture
Technology Selection and justification
Functional demonstration of major user story
****
GAN：Generator Adversrial Networks


##1. Definition of architecture:
For most of the Gan frames, there are normally a generator and a discriminator.

Responsibility:	 

Generator：to create simples which can cheat to the discriminator	  

Discriminator：to judge the simple which is created and which is real


Process：  Firstly，the generator will create a simple and send to the Discriminator and the discriminator will feedback with a grade and a gradient former. 
        Then the generator adjust the weight in algorithm accroding to the gradient former and generate a new one and repeat the process above. Eventually, 
        generator will create a image or data which can successfully cheat the discriminator.
        
Relationship: The generator and the discriminator are 2-player game in game theory. One aspect's winning means the other's lose.


##2.Technology Selection and justification:


Algorithm:
![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/341635780754_.pic_hd.jpg)

Tools: torchvision,pytorch
Dataset：MNIST     TFD      CIFAR-10

##3.Little eaxmple：

Image1 to Image2

original:
Which class in the module is 88.
https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a
![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/Choose-Ground-Cover-Plants-Step-5.jpeg)
****
after 1000training
![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/Figure_1.png)
****
after 10000training
![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/Figure_2.png)
****
after 20000training
![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/Figure_3.png)
  



