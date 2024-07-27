
import os
import cv2
import time
from concurrent.futures import ThreadPoolExecutor

# Function to process a single image: convert it to grayscale and save it
def process_image(image_path, output_dir):
try:
# Read the image from the specified path
image = cv2.imread(image_path)
if image is None:
print(f&quot;Failed to read image: {image_path}&quot;)
return

# Convert the image to grayscale
gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)

# Create the output path for the processed image
output_path = os.path.join(output_dir, os.path.basename(image_path))

# Save the grayscale image
cv2.imwrite(output_path, gray_image)
print(f&quot;Processed and saved image: {output_path}&quot;)
except Exception as e:
print(f&quot;Error processing image {image_path}: {e}&quot;)

# Function to process images using monothreading
def monothread_process(image_paths):
# Define the output directory for monothreaded processing

output_dir = &quot;output_monothread&quot;
if not os.path.exists(output_dir):
os.makedirs(output_dir)

start_time = time.time()
# Process each image one by one
for image_path in image_paths:
process_image(image_path, output_dir)
end_time = time.time()

print(f&quot;Monothreading: Processed {len(image_paths)} images in {end_time - start_time:.2f}
seconds&quot;)

# Function to process images using multithreading
def multithread_process(image_paths):
# Define the output directory for multithreaded processing
output_dir = &quot;output_multithread&quot;
if not os.path.exists(output_dir):
os.makedirs(output_dir)

start_time = time.time()
# Use ThreadPoolExecutor to process images in parallel
with ThreadPoolExecutor() as executor:
executor.map(lambda img_path: process_image(img_path, output_dir), image_paths)
end_time = time.time()

print(f&quot;Multithreading: Processed {len(image_paths)} images in {end_time - start_time:.2f}
seconds&quot;)

def main():

# Define the directory containing images to be processed
image_dir = &quot;images&quot;

# Get a list of image paths in the image directory
image_paths = [os.path.join(image_dir, img) for img in os.listdir(image_dir) if
img.endswith((&#39;.png&#39;, &#39;.jpg&#39;, &#39;.jpeg&#39;))]

if not image_paths:
print(&quot;No images found in the &#39;images&#39; directory.&quot;)
return

# Process images using monothreading and measure the time taken
print(&quot;Starting monothread processing...&quot;)
monothread_process(image_paths)

# Process images using multithreading and measure the time taken
print(&quot;Starting multithread processing...&quot;)
multithread_process(image_paths)

if __name__ == &quot;__main__&quot;:
main()
