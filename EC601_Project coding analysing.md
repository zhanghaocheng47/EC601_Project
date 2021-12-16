# EC601_Project coding analysing
#DCGAN coding analysing:

# 1. we used a better plan as DCGAN(Deep Convolution Generative Adversarial Networks)    (referrence:https://arxiv.org/abs/1511.06434)

For advantages: 
Gan’s result is indistinct(not good result)
In case of this, we tried to use a optimiser, according to the DCGAN paper, there’s a Adam optimiser included.
Besides, Gan’s process of training is not stable.

# 2. Difference or improvement from GAN module

After every time training,  batchnorm layer is put in to improve the stability
Different activation functions selected between D and G module
The utilization of Adam optimiser and control the learning rate of 0.0002
Use step-size convolution to replace the up-sampling layer. Convolution has a better effect on extracting image features
Convolution is used instead of the fully connected layer.

# 3.generator:
DCGAN generator

     class DCGAN_G(torch.nn.Module):
        def __init__(self):
        super(DCGAN_G, self).__init__()
        main = torch.nn.Sequential()

     # We need to know how many layers we will use at the beginning
     mult = param.image_size // 8

     ### Start block
     # Z_size random numbers
     main.add_module('Start-ConvTranspose2d', torch.nn.ConvTranspose2d(param.z_size, param.G_h_size * mult, kernel_size=4, stride=1, padding=0, bias=False))
     if param.SELU:
        main.add_module('Start-SELU', torch.nn.SELU(inplace=True))
     else:
        main.add_module('Start-BatchNorm2d', torch.nn.BatchNorm2d(param.G_h_size * mult))
        main.add_module('Start-ReLU', torch.nn.ReLU())
     # Size = (G_h_size * mult) x 4 x 4

     ### Middle block (Done until we reach ? x image_size/2 x image_size/2)
     i = 1
     while mult > 1:
        main.add_module('Middle-ConvTranspose2d [%d]' % i, torch.nn.ConvTranspose2d(param.G_h_size * mult, param.G_h_size * (mult//2), kernel_size=4, stride=2, padding=1, bias=False))
        if param.SELU:
           main.add_module('Middle-SELU [%d]' % i, torch.nn.SELU(inplace=True))
        else:
           main.add_module('Middle-BatchNorm2d [%d]' % i, torch.nn.BatchNorm2d(param.G_h_size * (mult//2)))
           main.add_module('Middle-ReLU [%d]' % i, torch.nn.ReLU())
        # Size = (G_h_size * (mult/(2*i))) x 8 x 8
        mult = mult // 2
        i += 1

     ### End block
     # Size = G_h_size x image_size/2 x image_size/2
     main.add_module('End-ConvTranspose2d', torch.nn.ConvTranspose2d(param.G_h_size, param.n_colors, kernel_size=4, stride=2, padding=1, bias=False))
     main.add_module('End-Tanh', torch.nn.Tanh())
     # Size = n_colors x image_size x image_size
     self.main = main

     def forward(self, input):
        if isinstance(input.data, torch.cuda.FloatTensor) and param.n_gpu > 1:
            output = torch.nn.parallel.data_parallel(self.main, input, range(param.n_gpu))
        else:
            output = self.main(input)
        return output
     
![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/dcgan.png)


# 4.discriminator
Cause the generator functions are all convTranspose2d, the discriminator should be in this case use conv2d.
There is another difference that the discriminator should adapt the Tanh activation function.

# 5.training:
The training process are the same but we should do the batchnorm operant.

# 6.result
10000:

![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/control_group.jpg)

100:

![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/100pics.png)

50:

![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/50pics.png)

cars:

![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/cars.png)

flowers:

![image](https://github.com/zhanghaocheng47/EC601_Project/blob/main/images/flowers.png)
