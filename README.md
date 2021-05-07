# Xdraw (work in progress)

A drawing program implemented in C++, rendered using OpenGL. The goal of the project was to make a performant drawing application. 

![demo wip](https://user-images.githubusercontent.com/22987416/117497712-add8fc80-af2d-11eb-8721-b40a00bcb22f.png)


Sample code:
This code sets the cursor given a certain radius for the brush tool.
 
```cpp
void set_cursor(GLFWwindow* window, int radius) { //colours are in little endian
	assert(radius > 0); // should always be above 0

	const int r = abs(radius);   // ensure radius is positive. Radius min/max is handled elsewhere.
	const int d = r*2 + 1;       // finding diameter, +1 to make odd to properly center cursor.
	const int d2 = d * d;

	std::vector<int> pixels;
	pixels.resize(d2);

	for (int i = 0; i < d2; i++) {
		
		int y = i / d;      // get y-coordinate
		int x = i - y * d;  // get x-coordinate
		y -= r;             // transform coordinates, s.t. 0,0 is the center of pixel grid.
		x -= r;

		float dist = length(vec2(x, y)); // dist = how far x,y is from pixel grid center.
		
		float inner_rad1 = r - 0.6f;
		float inner_rad2 = r - 1.0f;

		if (dist <= r && dist > inner_rad1) {
			pixels[i] = ~0; // set to white
		}
		else if (dist <= inner_rad1 && dist > inner_rad2) {
			pixels[i] = 0x2f000000; // transparent black
		}
		else {
			pixels[i] = 0; // transparent
		}
	}


	GLFWimage image;
	image.width = d;
	image.height = d;
	image.pixels = (unsigned char*)pixels.data();


	cursor = glfwCreateCursor(&image, r , r);
	glfwSetCursor(window, cursor);
	
}
