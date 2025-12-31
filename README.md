# Qwen2.5-VL-3B NPU
![Alt text](https://github.com/user-attachments/assets/6d297a34-c516-4cb1-be4a-bca471d40fa6)
<br><br>**User**:\<image\>Describe the image.<br><br>
**Answer**: The image depicts an astronaut floating in space, with Earth visible in the background. The astronaut is lying on their back and appears to be relaxing or sleeping. They have a beer bottle next to them, which adds a humorous touch to the scene. The setting includes a ladder leading up to what seems to be a spacecraft or moon landing module, suggesting that this image might be a creative or surreal take on space exploration.

------------

## Qwen2.5-3B VLM for RK3588 NPU (Rock 5, Orange Pi 5). <br/>
[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)<br/><br/>
Paper: [Qwen2.5 Technical Report](https://arxiv.org/pdf/2412.15115)<br/><br/>
Hugging face: https://huggingface.co/Qwen/Qwen2.5-VL-3B-Instruct

------------

## Introduction

LLMs (Large Language Models) are neural networks trained on large text datasets to understand and generate language.<br>
VLMs (Vision-Language Models) add a visual encoder so the model can process images and text together.<br> 
A combined VLM+LLM system is often referred to as a multimodal model.

These models can be large—hundreds of millions to billions of parameters—which impacts accuracy, memory use, and runtime speed.<br>
On edge devices like the RK3588, available RAM and compute are limited, and even the NPU has strict constraints on supported operations.<br>
Because of this, models typically need to be quantised or simplified to fit.

Performance is usually expressed in tokens (words) per second.<br>
Once converted to RKNN, parts of the model can run on the NPU, improving speed.<br>
Despite these limits, models like Qwen2.5 run well on the RK3588 because the NPU efficiently accelerates the heavy math, and the vision encoder can be optimised. This makes advanced multimodal AI feasible on small, power-efficient devices.

------------

## Model performance benchmark (FPS)

All models, with C++ examples, can be found on the Q-engineering GitHub.<br><br>
All LLM models are quantized to **w8a8**, while the VLM vision encoders use **fp16**.<br>

| model         | RAM (GB)<sup>1</sup> | llm cold sec<sup>2</sup> | llm warm sec<sup>3</sup> | vlm cold sec<sup>2</sup> | vlm warm sec<sup>3</sup> | Resolution | Tokens/s |
| --------------| :--: | :-----: | :-----: | :--------: | :-----: | :--------:  | :--------: |
| [Qwen3-2B](https://github.com/Qengineering/Qwen3-VL-2B-NPU) | 3.1 | 21.9 | 2.6 | 10.0  | 0.9 | 448 x 448 | 11.5 |
| [Qwen3-4B](https://github.com/Qengineering/Qwen3-VL-4B-NPU) | 8.7 | 49.6 | 5.6 | 10.6  | 1.1 | 448 x 448 | 5.7 |
| [Qwen2.5-3B](https://github.com/Qengineering/Qwen2.5-VL-3B-NPU) | 4.8 | 48.3 |  4.0 | 17.9  | 1.8 | 392 x 392 | 7.0 |
| [Qwen2-7B](https://github.com/Qengineering/Qwen2-VL-7B-NPU) | 8.7 | 86.6 |   34.5 | 37.1  | 20.7 | 392 x 392 | 3.7 |
| [Qwen2-2.2B](https://github.com/Qengineering/Qwen2-VL-2B-NPU) | 3.3 | 29.1 |   2.5 | 17.1  | 1.7 | 392 x 392 | 12.5 |
| [InternVL3-1B](https://github.com/Qengineering/InternVL3-NPU) | 1.3 |  6.8 |   1.1 | 7.8    | 0.75 | 448 x 448 | 30 |
| [SmolVLM2-2.2B](https://github.com/Qengineering/SmolVLM2-2B-NPU) | 3.4 | 21.2 |   2.6 | 10.5   | 0.9  | 384 x 384 | 11 |
| [SmolVLM2-500M](https://github.com/Qengineering/SmolVLM2-500M-NPU) | 0.8 |  4.8 |   0.7 | 2.5    | 0.25 | 384 x 384 | 31 |
| [SmolVLM2-256M](https://github.com/Qengineering/SmolVLM2-256M-NPU) | 0.5 |  1.1 |   0.4 | 2.5    | 0.25 | 384 x 384 | 54 |

<sup>1</sup> The total used memory; LLM plus the VLM. <br>
<sup>2</sup> When an llm/vlm model is loaded for the first time from your disk to RAM or NPU, it is called a cold start.<br>
The duration depends on your OS, I/O transfer rate, and memory mapping.<br> 
<sup>3</sup> Subsequent loading (warm start) takes advantage of the already mapped data in RAM. Mostly, only a few pointers need to be restored.<br><br>
<img width="600" height="450" alt="Plot_1" src="https://github.com/user-attachments/assets/6dbb7d8a-c55b-49b4-ae5a-ac272aade60b" /><br>
<img width="600" height="450" alt="Plot_2" src="https://github.com/user-attachments/assets/c4dd409b-2533-4ae0-942c-58fb3e8f5834" />

------------

## Dependencies.
To run the application, you have to:
- OpenCV 64-bit installed.
- rkllm library.
- rknn library.
- Optional: Code::Blocks. (```$ sudo apt-get install codeblocks```)

### Installing the dependencies.
Start with the usual 
```
$ sudo apt-get update 
$ sudo apt-get upgrade
$ sudo apt-get install cmake wget curl
```
#### OpenCV
To install OpenCV on your SBC, follow the Raspberry Pi 4 [guide](https://qengineering.eu/install-opencv-on-raspberry-64-os.html).<br><br>
Or, when you have no intentions to program code:
```
$ sudo apt-get install lib-opencv-dev 
```
------------

## Installing the app.
```
$ git clone https://github.com/Qengineering/Qwen2.5-VL-3B-NPU
```

#### RKLLM, RKNN
To run InternVL3, you need to have the **rkllm-runtime** library version **1.2.3** (or higher) installed, as well as the **rknpu driver** version **0.9.8**.<br>
If you don't have these on your machine, or if you have a lower version, you need to install them.<br>
We have provided the correct versions in the repo.<br>
```bash
$ cd ./Qwen2.5-VL-3B-NPU/aarch64/library
$ sudo cp ./*.so /usr/local/lib
$ cd ./Qwen2.5-VL-3B-NPU/aarch64/include
$ sudo cp ./*.h /usr/local/include
```
### Download the LLM and VLM model.
The next step is downloading the models.<br>
This time, we used the original model supplied by Rockchips [rkllm_model_zoo](https://console.box.lenovo.com/l/l0tXb8) (44 GB!), fetch code: rkllm.<br><br>
Better to download the two needed files (4.6 GB) from our Sync.com server:<br>
[qwen2.5-vl-3b-instruct_w8a8_rk3588.rkllm](https://ln5.sync.com/dl/4450f65a0#5xun8r2j-qeg5z6aw-k33ph6s7-eb7dgfbv) and [qwen2.5-vl-3b_vision_rk3588.rknn](https://ln5.sync.com/dl/68063c720#jadpjqg3-wgyca793-dfs8tv3u-qp2sgsnq)<br>
Copy both into this folder.




### Building the app.
Once you have the two models, it is time to build your application.<br>
You can use **Code::Blocks**.
- Load the project file *.cbp in Code::Blocks.
- Select _Release_, not Debug.
- Compile and run with F9.
- You can alter command line arguments with _Project -> Set programs arguments..._ 

Or use **Cmake**.
```
$ mkdir build
$ cd build
$ cmake ..
$ make -j4
```
### Running the app.
The app has the following arguments.
```bash
VLM_NPU Picture RKNN_model RKLLM_model NewTokens ContextLength
```
| Argument   | Comment |
| --------------| --  |
| picture | The image. Provide a dummy if you don't want to use an image | 
| RKNN_model | The visual encoder model (vlm) | 
| RKLLM_model | The large language model (llm) | 
| NewTokens | This sets the maximum number of new tokens. Optional, default 2048| 
| ContextLength | This specifies the maximum total number of tokens the model can process. Optional, default 4096| 

<br>In the context of the Rockchip RK3588 LLM (Large Language Model) library, the parameters NewTokens and ContextLength both control different limits for text generation, and they're typical in LLM workflows.<br>
**NewTokens**<br> 
This sets the maximum number of tokens (pieces of text, typically sub-word units) that the model is allowed to generate in response to a prompt during a single inference round. For example, if set to 300, the model will not return more than 300 tokens as output, regardless of the prompt length. It's important for controlling generation length to avoid too-short or too-long responses, helping manage resource use and output size.<br>
**ContextLength**<br>
This specifies the maximum total number of tokens the model can process in one go, which includes both the prompt (input) tokens and all generated tokens. For example, if set to 2048 and your prompt already uses 500 tokens, the model can generate up to 2048-500 = 1548 new tokens. This is a hardware and architecture constraint set during model conversion and deployment, as the context window cannot exceed the model's design limit (for instance, 4096 or 8192 tokens depending on the model variant).

A typical command line can be:
```bash
VLM_NPU ./Moon.jpg ./models/qwen2.5-vl-3b_vision_rk3588.rknn ./models/qwen2.5-vl-3b-instruct_w8a8_rk3588.rkllm 2048 4096
```
The NewTokens (2048) and ContextLength (4096) are optional and can be omitted.
### Using the app.
Using the application is simple. Once you provide the image and the models, you can ask everything you want.<br>
Remember, we are on a bare Rock5C, so don't expect the same quality answers as ChatGPT can provide.<br>
On the other hand, when you see the examples below, the app performs amazingly well.<br><br>
If you want to talk about the picture, you need to include the token `<image>` in your prompt once.<br>
The app remembers the dialogue until you give the token `<clear>`.<br>
With `<exit>`, you leave the application.
### C++ code.  
Below, you find the surprisingly little code of main.cpp. 
```cpp
#include "RK35llm.h"

int main(int argc, char** argv)
{
    std::string input_str;
    std::string output_str;
    RK35llm RKLLM;

    RKLLM.SetInfo(true);            //yes, you may give me additional model information
    RKLLM.SetSilence(false);        //you may print the incremental text chunks on the terminal

    if     (argc< 4) {std::cerr << "Usage: " << argv[0] << " image vlm_model llm_model [option]NewTokens [option]ContextLength\n"; return -1;}
    else if(argc==4) RKLLM.LoadModel(argv[2],argv[3]);
    else if(argc==5) RKLLM.LoadModel(argv[2],argv[3],std::atoi(argv[4]));
    else if(argc> 5) RKLLM.LoadModel(argv[2],argv[3],std::atoi(argv[4]),std::atoi(argv[5]));

    cv::Mat Pic = cv::imread(argv[1]);
    RKLLM.LoadImage(Pic);

    while(true) {
        printf("\n");
        printf("User: ");

        std::getline(std::cin, input_str);
        if (input_str == "exit") break;

        output_str = RKLLM.Ask(input_str);
//        std::cout << "\nLLM Reply: " << output_str << std::endl;
    }

    return 0;
}
```
Most code speaks for itself. One remark.<br>
The LLM generates the answer to your request in little pieces of text. You see them as if the LLM is typing on your terminal.<br>
RKLLM.SetSilence controls this behaviour. When set to `true`, it will suppress this output. RKLLM.Ask() also returns the complete answer.<br>
In main.cpp uncomment this line:
```cpp
std::cout << "\nLLM Answer: " << output_str << std::endl;
```

------------

## VLM examples.
| Image         | Dialog |
| --------------| --  |
| ![Singapore_S](https://github.com/user-attachments/assets/e742059a-3bdb-41be-9647-e7767da12ce6) |**User**: \<image\>Which city is this?<br>**Answer**: This image depicts the skyline of Singapore, featuring iconic landmarks such as Marina Bay Sands and the Singapore Flyer Ferris wheel. The reflection in the water adds to the picturesque quality of the scene.| 
| ![ChineseWall_S](https://github.com/user-attachments/assets/3ba4b57a-4b22-4050-b995-9599c6d239de) |**User**: \<image\>Tell me something about the history of this place.<br>**Answer**: The image you provided appears to be of the Great Wall of China, specifically the section known as the Jinshanling Great Wall in Hebei Province, China. The Great Wall is one of the most iconic and recognizable landmarks in Chinese culture.<br><br>History of the Great Wall:<br>1. **Ancient Origins (2nd Century BC - 3rd Century AD):**<br>- The earliest sections of the Great Wall were built during the Warring States period (475-221 BCE) to protect against invasions from nomadic tribes.<br>- The first emperor, Qin Shi Huang, unified China and ordered the construction of a more extensive wall starting in 220 BCE.<br>2. **Qin Dynasty (221-206 BCE):**<br>- The Great Wall was initially built as a series of fortifications along the northern border to protect against invasions from nomadic tribes.<br>- It stretched for over 7,000 miles but was largely destroyed by the end of the Qin dynasty.<br>3. **Han Dynasty (206 BCE – 220 CE):**<br>- The Han dynasty continued the construction and expansion of the Great Wall.<br>- The wall reached its maximum length during this period, stretching over 15,000 miles.<br>4. **Wei, Jin, and Northern and Southern Dynasties (220-589 CE):**<br>- During these periods, parts of the wall were abandoned or destroyed due to frequent invasions.<br>- The wall was repaired sporadically but never fully rebuilt in its entirety.<br>5. **Sui and Tang Dynasties (581–756 CE):**<br>- The Sui dynasty briefly restored some sections of the Great Wall, but it was largely abandoned again by the Tang dynasty.<br>- During this period, the wall became more of a symbolic structure rather than a functional defense.<br>6. **Ming Dynasty (1368-1644 CE):**<br>- The Ming dynasty undertook extensive construction and expansion of the Great Wall in the 17th century to protect against invasions from Manchu tribes.<br>- The most famous section, Jinshanling, was built during this period.<br><br>Modern Significance:<br>Today, the Great Wall is a UNESCO World Heritage Site and one of China's top tourist attractions. It serves as a symbol of Chinese history, culture, and resilience. Despite its age and historical significance, parts of it remain in ruins or are under construction due to ongoing restoration efforts.<br><br>The Jinshanling section, shown in your image, is particularly well-preserved and offers visitors stunning views of the surrounding landscape, making it a popular destination for tourists interested in history and nature.|
| ![Pizza_S](https://github.com/user-attachments/assets/26fd5da3-f491-4b48-978a-14eae0819247) |**User**: \<image\>What are the ingredients I need to make this?<br>**Answer**: To make a Margherita pizza, you will need the following ingredients:<br><br>1. **Dough**:<br>- All-purpose flour<br>- Warm water<br>- Yeast (active dry or instant)<br>- Sugar<br>- Salt<br>2. **Tomato Sauce**:<br>- Tomatoes (fresh or canned)<br>- Olive oil<br>- Garlic (optional, for flavoring the sauce)<br>3. **Cheese**:<br>- Mozzarella cheese (shredded or sliced)<br>4. **Herbs and Garnishes**:<br>- Fresh basil leaves<br>5. **Optional Additions**:<br>- Salt and pepper to taste<br>- Olive oil for greasing the pizza stone or baking sheet<br><br>Preparation Steps:<br>1. **Prepare the Dough:**<br>  - In a large bowl, combine warm water with sugar and yeast.<br>- Let it sit until the yeast becomes active (about 5-10 minutes).<br>- Add flour, salt, and any other desired ingredients to the yeast mixture.<br>- Knead the dough on a floured surface until it is smooth and elastic.<br>2. **Prepare the Tomato Sauce:**<br>- If using fresh tomatoes, blend them with olive oil and garlic (if using) in a food processor or blender.<br>- Season with salt and pepper to taste.<br>3. **Assemble the Pizza:**<br>- Preheat your oven to 500°F (260°C).<br>- Grease a pizza stone or baking sheet with olive oil.<br>- Roll out the dough into a circle that fits your pan.<br>- Transfer the dough to the greased surface and stretch it slightly if needed.<br>4. **Add Cheese and Sauce:**<br>- Spread the tomato sauce evenly over the dough, leaving about 1/2 inch around the edge.<br>- Sprinkle mozzarella cheese on top of the sauce.<br>5. **Garnish with Basil Leaves:**<br>- Place fresh basil leaves on top of the cheese.<br>6. **Bake the Pizza:**<br>- Slide the pizza onto the preheated pizza stone or baking sheet.<br>- Bake for about 10-12 minutes, or until the crust is golden brown and the cheese is bubbly and slightly browned.<br>7. **Serve:**<br>- Remove from the oven and let it cool for a few minutes before slicing and serving.<br><br>Enjoy your homemade Margherita pizza!<br><br>**User**: What is the first step?<br>**Answer**: The first step in making a Margherita pizza is to prepare the dough. Here are the detailed steps:<br><br>1. **Prepare the Dough:**<br>- In a large bowl, combine warm water with sugar and yeast.<br>- Let it sit until the yeast becomes active (about 5-10 minutes).<br>- Add flour, salt, and any other desired ingredients to the yeast mixture.<br>- Knead the dough on a floured surface until it is smooth and elastic.<br><br>This initial step sets the foundation for your pizza by creating the base that will support all the toppings.|
| ![Titanic_S](https://github.com/user-attachments/assets/bf6da76c-c9a4-41c6-bea1-2b4411dd4013) |**User**: \<image\>Which movie is this?<br>**Answer**: The image depicts a scene from the movie "Titanic." The characters in the picture are Leonardo DiCaprio and Kate Winslet, who played Jack Dawson and Rose DeWitt Bukater respectively. This iconic scene is one of the most famous moments in the film, where Jack and Rose are caught in a storm on the deck of the RMS Titanic as it sinks.|

## LLM example.
Notice the nice Markdown makeup of the answer!<br><br>
**User**: Can you make me a C++ call that embeds the Pythagorean theorem?<br><br>
**Answer**: Certainly! The Pythagorean theorem states that in a right-angled triangle, the square of the length of the hypotenuse (the side opposite the right angle) is equal to the sum of the squares of the lengths of the other two sides. Here's a simple C++ routine that implements this:

```cpp
#include <iostream>
#include <cmath> // For sqrt function

// Function to calculate the square root using std::sqrt
double sqrt(double x) {
    return std::sqrt(x);
}

// Function to check if three numbers form a right-angled triangle
bool isRightTriangle(int a, int b, int c) {
    double side1 = (a > b ? a : b); // Ensure the largest number is 'side1'
    double side2 = (b > c ? b : c);
    double hypotenuse = sqrt(side1 * side1 + side2 * side2);

    return std::abs(hypotenuse - c) < 0.0001; // Allow for a small floating-point error
}

int main() {
    int a, b, c;

    // Input the lengths of the sides
    std::cout << "Enter the lengths of the two shorter sides (a and b): ";
    std::cin >> a >> b;
    
    // Check if they form a right-angled triangle
    if (isRightTriangle(a, b, c)) {
        std::cout << "The triangle with sides " << a << ", " << b << ", and " << c << " is a right-angled triangle." << std::endl;
    } else {
        std::cout << "The triangle with sides " << a << ", " << b << ", and " << c << " is not a right-angled triangle." << std::endl;
    }

    return 0;
}
```

### Explanation:
1. **`sqrt(double x)`**: This function calculates the square root of `x`. It uses `<cmath>` for its implementation.
2. **`isRightTriangle(int a, int b, int c)`**: This function checks if three given integers form a right-angled triangle using the Pythagorean theorem. The largest number is considered as the hypotenuse (`side1`). If the square of the hypotenuse equals the sum of squares of the other two sides within a small tolerance (0.0001), it returns `true`, indicating that they form a right-angled triangle.
3. **`main()`**: This function prompts the user to input the lengths of the two shorter sides (`a` and `b`) and checks if these three numbers can form a right-angled triangle using the `isRightTriangle` function.

### Usage:
1. Compile the code with a C++ compiler (e.g., `g++ -o pythagorean pythagorean.cpp`).
2. Run the compiled program.
3. Enter the lengths of the two shorter sides when prompted, and it will tell you if they form a right-angled triangle.

This routine is simple but effective for demonstrating how to use basic mathematical functions in C++.

------------

## **[Rock5GPT](https://rock5gpt.qengineering.eu)**
To get a taste, try our professional Qwen3 AI-chatbot running on a Rock 5C: https://rock5gpt.qengineering.eu
<img width="815" height="1151" alt="Rock5GPT" src="https://github.com/user-attachments/assets/3ce5ad31-bc2b-4513-8ac9-42be793a86db" /><br>


------------

[![paypal](https://qengineering.eu/images/TipJarSmall4.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=CPZTM5BB3FCYL) 




