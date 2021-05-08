# Xdraw (work in progress)

A drawing program implemented in C++, rendered using OpenGL. The goal of the project was to make a performant drawing application. 

![demo wip](https://user-images.githubusercontent.com/22987416/117497712-add8fc80-af2d-11eb-8721-b40a00bcb22f.png)


Sample code:
This code sets the cursor given a certain radius for the brush tool.
 
```cpp

//radius constants
constexpr int max_radius = 500;
constexpr int min_radius = 1;

//offsets for cursor
constexpr float middle_offset = 0.6f;
constexpr float inner_offset = 1.0f;

//colours for cursor
constexpr int transparent_black = 0x2f000000;
constexpr int transparent = 0;
constexpr int white = ~0;

...

void set_cursor(GLFWwindow* window, int radius) { //colours are in little endian
	assert(radius >= min_radius && radius <= max_radius); // check for debugging purposes

	const int r = std::clamp(radius, min_radius, max_radius); // extra check for correct range
	const int d = r*2 + 1;    // finding diameter, +1 to make odd, to properly center cursor.
	const int d2 = d * d;

	std::vector<int> pixels; 
	pixels.resize(d2); // dxd grid of pixels, 32-bit RGBA

	for (int i = 0; i < d2; i++) {
		int y = i / d;      // get y-coordinate
		int x = i - y * d;  // get x-coordinate
		y -= r;             // transform coordinates, s.t. 0,0 is the center of pixel grid.
		x -= r;

		float dist = length(vec2(x, y)); // dist = how far x,y is from pixel grid center.
		
		float middle_radius = r - middle_offset;
		float inner_radius = r - inner_offset;

		if (dist <= r && dist > middle_radius) {
			pixels[i] = white;
		}
		else if (dist <= middle_radius && dist > inner_radius) {
			pixels[i] = transparent_black;
		}
		else {
			pixels[i] = transparent;
		}
	}


	GLFWimage image;
	image.width = d;
	image.height = d;
	image.pixels = (unsigned char*)pixels.data();


	cursor = glfwCreateCursor(&image, r , r);
	glfwSetCursor(window, cursor);
}

