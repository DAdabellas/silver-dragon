# silver-dragon
Beta
import os
import random
import subprocess
from glob import glob

def generate_video_from_random_images(images_directory, output_video_path, num_images=5, frame_rate=1):
    """
    Generate a video from a random selection of images.
    
    Parameters:
        images_directory (str): Path to the directory containing images.
        output_video_path (str): Path where the generated video will be saved.
        num_images (int): Number of images to include in the video.
        frame_rate (int): Frame rate of the video in FPS.
    """
    # Get all images from the directory
    all_images = glob(os.path.join(images_directory, '*.jpg')) # Assuming images are in JPG format
    if not all_images:
        raise ValueError("No images found in the directory.")
    
    # Select a random subset of images
    selected_images = random.sample(all_images, min(num_images, len(all_images)))
    
    # Generate a temporary file listing the images to include in the video
    with open("temp_filelist.txt", "w") as filelist:
        for image in selected_images:
            filelist.write(f"file '{image}'\n")
            filelist.write(f"duration {1/frame_rate}\n")  # Show each image for 1/frame_rate seconds
    
    # Use ffmpeg to create the video from the images listed in the temporary file
    subprocess.run(["ffmpeg", "-f", "concat", "-safe", "0", "-i", "temp_filelist.txt", "-vsync", "vfr", "-pix_fmt", "yuv420p", output_video_path], check=True)
    
    # Cleanup
    os.remove("temp_filelist.txt")

# Example usage
images_dir = "./rocket_images/"
output_video = "./rocket_video.mp4"
generate_video_from_random_images(images_dir, output_video)