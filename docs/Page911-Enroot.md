# Enroot for HPC and Deep Learning Applications

##### Introduction to Enroot

<b>What is Enroot ?</b> Enroot is a lightweight containerization technology designed to provide simple, efficient, and secure container execution in High-Performance Computing (HPC) environments. It allows users to run containerized workloads with minimal overhead and maximum portability.

##### Why Use Enroot for Deep Learning in HPC?

- <b> Compatibility with HPC systems: </b> Enroot integrates seamlessly with HPC schedulers like SLURM, avoiding the need for privileged operations.

- <b> Lightweight execution: </b> Enroot has minimal runtime overhead, focusing on simplicity and performance.

- <b> Security: </b> Unlike Docker, which requires root privileges, Enroot operates without elevated permissions, making it safer in shared environments.

- <b> GPU Acceleration: </b> Enroot supports NVIDIA GPUs, a critical feature for deep learning tasks.

- <b> Filesystem Isolation: </b> Enroot uses squashfs images, ensuring efficient storage and access to containerized environments

##### Basic Commands in Enroot

Here are some commonly use Enroot commands:

<table>
  <thead>
    <tr>
      <th>Command</th>
      <th>Explanation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>enroot import docker://&lt;image&gt;</code></td>
      <td>Downloads a Docker container image and converts it into an Enroot-compatible squashfs image.</td>
    </tr>
    <tr>
      <td><code>enroot list</code></td>
      <td>Lists all available Enroot container images in the system.</td>
    </tr>
    <tr>
      <td><code>enroot create &lt;image&gt;</code></td>
      <td>Creates a container from the specified squashfs image.</td>
    </tr>
    <tr>
      <td><code>enroot start &lt;container&gt;</code></td>
      <td>Starts the specified container and enters its shell.</td>
    </tr>
    <tr>
      <td><code>enroot start --mount=&lt;src&gt;:&lt;dst&gt; &lt;container&gt;</code></td>
      <td>Starts the container with a host directory mounted inside.</td>
    </tr>
    <tr>
      <td><code>enroot remove &lt;container&gt;</code></td>
      <td>Deletes a container from the system.</td>
    </tr>
    <tr>
      <td><code>enroot exec &lt;container&gt; &lt;command&gt;</code></td>
      <td>Executes a single command within the specified container.</td>
    </tr>
  </tbody>
</table>

##### Sample SLURM Script with Enroot

Below is an example SLURM script that demonstrates how to download a container image, open it, and run a simple "Hello, World!" program.

```
#!/bin/bash
#SBATCH --job-name=enroot_example        # Job name
#SBATCH --output=hello_world_output.log  # Standard output and error log
#SBATCH --partition=gpu                  # Partition to use (adjust to your HPC setup)
#SBATCH –-gres gpu:1                      # Number of GPUs required
#SBATCH -n 1					   # Number of nodes to be allocated
#SBATCH --time=00:10:00                  # Maximum run time

# Step 1: Import a container image
echo "Downloading container image..."
enroot import docker://ubuntu:20.04

# Step 2: Create a container
echo "Creating the container..."
enroot create ubuntu+20.04.sqsh

# Step 3: Run a simple program in the container
echo "Starting the container and running 'Hello, World!'..."
enroot exec ubuntu+20.04 /bin/bash -c 'echo "Hello, World from Enroot!"'
```

##### How to Run the SLURM Script

- Save the script as enroot_hello_world.sh.
- Submit the script using the sbatch command: 
- sbatch enroot_hello_world.sh
- Check the output in the hello_world_output.log file once the job is complete

##### Conclusion

Enroot simplifies running containerized workloads in HPC environments while providing essential features like GPU support and enhanced security. With its minimalistic approach, Enroot is ideal for deep learning applications that require high performance and integration with HPC schedulers like SLURM.

## Programs using DL modules

1 . TensorFlow Script for Training a CNN on CIFAR-10

<b> Python Script (train_cnn_tf.py) </b>

```
import tensorflow as tf
from tensorflow.keras import layers, models
from tensorflow.keras.datasets import cifar10
from tensorflow.keras.utils import to_categorical

# Load and preprocess the CIFAR-10 dataset
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0
y_train, y_test = to_categorical(y_train), to_categorical(y_test)

# Build a simple CNN model
model = models.Sequential([
    layers.Conv2D(32, (3, 3), activation='relu', input_shape=(32, 32, 3)),
    layers.MaxPooling2D((2, 2)),
    layers.Conv2D(64, (3, 3), activation='relu'),
    layers.MaxPooling2D((2, 2)),
    layers.Conv2D(64, (3, 3), activation='relu'),
    layers.Flatten(),
    layers.Dense(64, activation='relu'),
    layers.Dense(10, activation='softmax')
])

# Compile the model
model.compile(optimizer='adam',
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# Train the model
model.fit(x_train, y_train, epochs=10, batch_size=64, validation_data=(x_test, y_test))

# Save the model
model.save("cnn_cifar10_tf.h5")
```

##### SLURM Script for TensorFlow (run_tf_cnn.sh)

```
#!/bin/bash
#SBATCH --job-name=tf_cnn_cifar10         # Job name
#SBATCH --output=tf_cnn_output.log        # Standard output and error log
#SBATCH --partition=gpu                   # Partition to use
#SBATCH -n 1                          	    # Number of nodes
#SBATCH –-gres gpu:1 				    # Required number of GPUs
#SBATCH --time=01:00:00                   # Maximum run time

# Load the TensorFlow module or activate your environment
module load mldl_modules/tensorflow_gpu  # Example, adapt to your HPC setup

# Run the training script
python train_cnn_tf.py
```

2 . PyTorch Script for Training a CNN on CIFAR-10

<b> Python Script (train_cnn_pytorch.py) </b>

```
import torch
import torch.nn as nn
import torch.optim as optim
from torchvision import datasets, transforms

# Define a simple CNN model
class CNN(nn.Module):
    def __init__(self):
        super(CNN, self).__init__()
        self.conv1 = nn.Conv2d(3, 32, kernel_size=3, activation='relu')
        self.conv2 = nn.Conv2d(32, 64, kernel_size=3, activation='relu')
        self.fc1 = nn.Linear(64 * 6 * 6, 64)
        self.fc2 = nn.Linear(64, 10)

    def forward(self, x):
        x = nn.ReLU()(self.conv1(x))
        x = nn.MaxPool2d(2)(x)
        x = nn.ReLU()(self.conv2(x))
        x = nn.MaxPool2d(2)(x)
        x = torch.flatten(x, 1)
        x = nn.ReLU()(self.fc1(x))
        return self.fc2(x)

# Preprocess the CIFAR-10 dataset
transform = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
])

train_dataset = datasets.CIFAR10(root='./data', train=True, download=True, transform=transform)
test_dataset = datasets.CIFAR10(root='./data', train=False, download=True, transform=transform)

train_loader = torch.utils.data.DataLoader(train_dataset, batch_size=64, shuffle=True)
test_loader = torch.utils.data.DataLoader(test_dataset, batch_size=64, shuffle=False)

# Initialize the model, loss function, and optimizer
model = CNN().cuda()
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=0.001)

# Train the model
for epoch in range(10):
    model.train()
    for images, labels in train_loader:
        images, labels = images.cuda(), labels.cuda()

        optimizer.zero_grad()
        output = model(images)
        loss = criterion(output, labels)
        loss.backward()
        optimizer.step()

# Save the model
torch.save(model.state_dict(), "cnn_cifar10_pytorch.pth")
```

##### SLURM Script for PyTorch (run_pytorch_cnn.sh)

```
#!/bin/bash
#SBATCH --job-name=pytorch_cnn_cifar10    # Job name
#SBATCH --output=pytorch_cnn_output.log   # Standard output and error log
#SBATCH --partition=gpu                   # Partition to use
#SBATCH –-gres gpu:1                      # Number of GPUs
#SBATCH --time=01:00:00                   # Maximum run time
#SBATCH – n 1                         	    # Number of nodes to be allocated

# Load the PyTorch module or activate your environment
module load mldl_modules/pytorch_gpu  # Example, adapt to your HPC setup

# Run the training script
python train_cnn_pytorch.py
```