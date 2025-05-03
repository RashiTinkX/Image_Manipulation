```markdown
# Image Scene Composition Script

This repository contains a Python script designed to compose a simple image scene by combining different image assets using the **Pillow (PIL) library**.

## Description

The script automates the process of creating a composite image by taking separate image files for a wall, a floor, a car (with a mask for transparency), and a shadow mask, then combining and positioning them to create a final scene.

The key steps performed by the script are:

1.  **Combining Background**: It merges a wall image and a floor image into a single background image.
2.  **Positioning the Car**: It resizes a car image and places it onto the combined background using a mask to handle transparency and shape.
3.  **Adding a Shadow**: It resizes a shadow mask, applies adjustments such as opacity and blur, colorizes it, and pastes it onto the scene, positioned under the car.

The script outputs intermediate images at major stages, as well as the final composite image.

## Requirements

*   **Python 3.x**
*   **Pillow** library (PIL)

You can install Pillow using pip:
```bash
pip install Pillow
```
*   The script includes code for mounting **Google Drive**, suggesting it may be intended for execution in a **Google Colab environment**. If running locally, the Google Drive part is not needed.

## Input Files

The script requires the following image files to be present in the same directory as the script or accessible via the specified paths:

*   `wall.png`: Image intended for the wall part of the background.
*   `floor.png`: Image intended for the floor part of the background.
*   `car_mask_6.png`: A grayscale mask image defining the shape and transparency of the car. It is opened and converted to grayscale ("L").
*   `car_6.jpeg`: The original colour image of the car. It is opened and converted to "RGBA".
*   `6.png`: A grayscale mask image used to define the shape of the shadow. It is opened and converted to grayscale ("L").

*Note: The file names like `car_mask_6.png`, `car_6.jpeg`, and `6.png` are specific to the example shown in the code. You would need to replace these with the paths to your own corresponding image files.*

## Output Files

The script generates the following files:

*   `combined_wall_floor_background.png`: The result of combining the wall and floor images.
*   `final_image_peeka.png`: The combined background with the resized and positioned car.
*   `car_with_shadow_output.png`: The final scene with the car and its shadow.

## How to Run

1.  Ensure you have Python and Pillow installed.
2.  Place the input image files (`wall.png`, `floor.png`, `car_mask_6.png`, `car_6.jpeg`, `6.png`) in the same directory as the script, or update the file paths within the script.
3.  Run the Python script from your terminal:
    ```bash
    python your_script_name.py
    ```
    (Replace `your_script_name.py` with the actual name of the Python file containing the code).
4.  If running in Google Colab, the final lines will prompt you to authenticate and **mount your Google Drive**. You may need to adjust file paths if your images are stored on Drive.

The script will display the generated images as it progresses.

## Script Breakdown

The script executes in three main stages using functions from the **PIL.Image** module, and others like **ImageEnhance**, **ImageOps**, and **ImageFilter**:

1.  **Background Composition**:
    *   Opens and converts `wall.png` and `floor.png` to "RGBA".
    *   Resizes both images to match the maximum width.
    *   Creates a new blank image with the combined height and maximum width.
    *   Pastes the resized wall at the top (`(0, 0)`).
    *   Pastes the resized floor just below it (`(0, wall.height)`).
    *   Saves the result as `combined_wall_floor_background.png`.
    *   Shows the combined background image.

2.  **Car Placement**:
    *   Opens `car_mask_6.png` as a grayscale ('L') mask and `car_6.jpeg` as "RGBA".
    *   Opens the `combined_wall_floor_background.png` as "RGBA".
    *   Retrieves dimensions of the background and car mask.
    *   Defines a `scaling_factor` (set to **2.50** in the code) to enlarge the car size.
    *   Calculates the `new_car_size` based on the mask size and scaling factor.
    *   **Resizes the car image** and **its mask** to `new_car_size` using `Image.Resampling.LANCZOS` for quality.
    *   Calculates horizontal (`x_offset`) and vertical (`y_offset`) offsets to position the car. The `x_offset` centers the car horizontally, while the `y_offset` is calculated as `bg_height // 12` to position it on the floor.
    *   **Pastes the resized car** onto the background at the calculated offsets, crucially using the `resized_car_mask` as the `mask` parameter to respect the car's shape and transparency.
    *   Saves this intermediate result as `final_image_peeka.png`.
    *   Shows the image with the car placed.

3.  **Shadow Addition**:
    *   Loads the `final_image_peeka.png` (the image with the car on the background).
    *   Loads `6.png` as a grayscale ('L') shadow mask.
    *   **Resizes the shadow mask** using a `shadow_scale_factor` (also **2.50** in the code). `Image.Resampling.LANCZOS` is used for resizing.
    *   Adjusts the shadow's opacity using `ImageEnhance.Brightness` with a `shadow_opacity` factor (**0.7** in the code).
    *   Applies a **Gaussian blur** to the resized shadow mask with a `radius` of **5** using `ImageFilter.GaussianBlur` to soften the edges.
    *   Converts the blurred shadow mask to "RGBA" and **colorizes it solid black** using `ImageOps.colorize`.
    *   Calculates offsets to position the shadow under the car. The horizontal offset centers the shadow relative to the car image. The vertical offset is calculated as `car_image.height - shadow_resized.height + 100` to align it with the car base.
    *   Creates a copy of the image with the car.
    *   **Pastes the colorized shadow image** onto the image copy, using the original grayscale `shadow_resized` mask (before colorization and blurring) as the `mask` parameter for the paste operation. This ensures the shadow's shape and transparency are controlled by the mask.
    *   Saves the final output as `car_with_shadow_output.png`.
    *   Shows the final image with the shadow.

## Customisation

You can modify the script to adjust the scene composition and appearance:

*   **File Paths**: Change the `_path` variables (`wall_path`, `floor_path`, `car_mask_path`, `car_image_path`, `background_path`, `shadow_mask_path`, `output_image_path`) to use different input or output images.
*   **Scaling**: Adjust the `scaling_factor` to change the size of the car and the `shadow_scale_factor` to change the size of the shadow. Both are currently set to **2.50**.
*   **Positioning**: Modify the `x_offset` and `y_offset` calculations in the car placement and shadow addition sections to move the car and shadow. The car's `y_offset` is currently `bg_height // 12`, and the shadow's is `car_image.height - shadow_resized.height + 100`.
*   **Shadow Appearance**: Change the `shadow_opacity` (between 0 and 1, lower is darker) and the `radius` in `ImageFilter.GaussianBlur` to adjust the shadow's darkness and softness. Opacity is currently **0.7**, and blur radius is **5**.
*   **Resampling Filter**: While `LANCZOS` is used for resizing the car and shadow, you could potentially explore other `Image.Resampling` filters provided by Pillow if needed.

This README provides an overview of the provided image manipulation script, its requirements, and how to use it, based on the actions and parameters found in the source code.
```
