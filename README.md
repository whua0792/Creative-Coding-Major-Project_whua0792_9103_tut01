## Major Project_Wensi Huang_whua0792_9103_tut01

### __Instructions on how to interact with the work:__

- AThe animation will start automatically when the file is opened. 
- You can watch the graphics float slowly for a while. 
- Afterward, you can click the screen, and some of the graphics will scatter.
- Click again to stop the spreading, and click once more to resume it. 
- To return to the original state, simply refresh the screen. (The meaning of this interaction will be explained later.)

### __Details of your individual approach to animating the group code.__
#### I chose the Perlin noise and randomness to drive my individual code.

#### Animation properties:
- I chose to have each dynamic circle ring expand, contract, and float up and down to simulate a jellyfish-like rhythm, enhanced by a semi-transparent effect. Additionally, I added a click interaction where, upon each click, some rings disperse into particles that gradually expand outward, symbolizing the irreversible fading of memories. Finally, I created a looping background effect with a continuous transition of seven colors, representing the richness and vibrancy of memories.

#### Unique Aspects：
- Unlike the work of other team members, my animation focuses more on the dynamic growth and irreversible nature of each 'memory.' These rings do not change based on audio or time, as in other team members' works, but instead showcase 'living, natural floating' driven by Perlin Noise. When the user clicks, the dispersal effect of the dynamic rings symbolizes how memories scatter upon touch, and their irreversible nature highlights the fragility of memories.

###  __Inspiration and How It Influenced Your Code__
#### Inspiration：
- Inspired by moon jellyfish, their translucent and rhythmic breathing qualities sparked my creativity, making the memory rings float in the background like jellyfish.

#### Influence：
- The lightness and rhythmic movement of jellyfish inspired my application of Perlin noise. I used Perlin noise in the code to simulate the rhythmic ‘breathing’ of the jellyfish, allowing the rings to subtly expand, contract, and shift positions. Meanwhile, the contents inside the jellyfish in my design are represented by a gradually dispersing, randomly distributed particle effect, simulating how memories gradually fade and ultimately disappear when touched.

> [Moon Jellyfish Youtube Video](https://www.youtube.com/watch?v=MoGTUPVhFVw&t=16262s)

> moon jellyfish
![An image of moon jellyfish in purple and red colors](https://www.worldatlas.com/r/w768-q80/upload/34/9a/78/shutterstock-1393231877.jpg)

> moon jellyfish
![An image of moon jellyfish in blue and green colors](https://live.staticflickr.com/8385/28578669684_4f709a6e65_b.jpg)

### __Technical explanation__
#### How My Code Works：
- My code mainly relies on Perlin noise to control the "breathing" rhythm of the rings, combined with randomness to produce the scattering particle effect when clicked. I apply Perlin noise to the position and size of the rings, allowing their expansion and contraction to occur at a natural and irregular frequency. Meanwhile, the background scrolling effect simulates an underwater dreamlike scene with a looping color band.

#### Code Changes from Group Code：
1.  #### Dynamic Circle Ring：
I use the noise() function to add randomness to the coordinates and size of the rings, creating subtle floating effects.
 ```
 // Apply Perlin noise to create smooth movement for x and y positions
     this.x += map(noise(this.noiseOffsetX), 0, 10, 0, 1000); // Smooth horizontal motion
     this.y += map(noise(this.noiseOffsetY), 0, 10, 0, 1000); // Smooth vertical motion
// Apply Perlin noise to control the size (cirSize) of the circle
     this.cirSize = map(noise(this.noiseOffsetSize), 0, 1, 100, 200); // Map noise to a range for circle size (50 to 150)
```

2. #### Mousepress interaction：
The mouse-press interaction in the code converts some of the circles into particles with each click, which scatter outward and gradually fade away.
```
function mousePressed() {
  // Toggle the state when mouse is clicked
  isNoisyState = !isNoisyState;
}
```

```
display() {
    if (!isNoisyState) {
      // Original state: just draw a stationary particle
      strokeWeight(this.sw);
      stroke(this.r, this.g, this.b, this.alp);
      point(this.x, this.y);
    } else {
      // Noisy state: apply Perlin noise and random variations
      // Update position with Perlin noise to create smooth movement
      let perlinX = map(noise(this.noiseOffsetX), 0, 1, -1, 1);
      let perlinY = map(noise(this.noiseOffsetY), 0, 1, -1, 1);

      // Apply the random changes based on Perlin noise
      this.x += perlinX * 2; // Use noise for horizontal movement
      this.y += perlinY * 2; // Use noise for vertical movement

      // Optionally, add randomness to the size of the stroke over time
      this.sw = 4 + random(-1, 1) * this.randomSizeFactor;

      // Optionally, add randomness to the opacity for visual effect
      this.alp = this.alp * random(0.8, 1.2);

      // Set stroke properties
      strokeWeight(this.sw);
      stroke(this.r, this.g, this.b, this.alp);

      // Draw the particle as a point
      point(this.x, this.y);

      // Update Perlin noise offsets for next frame
      this.noiseOffsetX += this.noiseSpeed;
      this.noiseOffsetY += this.noiseSpeed;
    }
  }
```
3. #### Rainbow background transition：
The background uses lerpColor() to create a smooth rainbow gradient transition between colors, enhancing the richness and dreamlike quality of the scene.
```
  colors = [
    color(10, 30, 90),  // dark indigo-blue
    color(1, 17, 92),   // dark blue
    color(60, 6, 102),  // dark purple
    color(120, 8, 90),  // dark red
    color(140, 50, 50), // dark orange
    color(80, 100, 20), // dark yellow-green
    color(0, 60, 50),   // dark teal-green
    color(10, 30, 90)   // dark indigo-blue
  ];
```
```
  // Increase the offset for scrolling effect
  offsetY += scrollSpeed;
  if (offsetY >= colorBandHeight  / scaleFactor * colors.length) {
    offsetY = 0; // Loop the offset
  }

  // Draw the gradient
  for (let y = 0; y < height  / scaleFactor; y++) {
    // Calculate adjusted position with offsetY and wrap around
    let adjustedY = (y + offsetY) % (colorBandHeight  / scaleFactor * colors.length);
    let inter = map(adjustedY, 0, colorBandHeight  / scaleFactor * colors.length, 0, 1);

    // Select two colors to blend based on position
    let colorIndex = floor(inter * (colors.length - 1)); // This technique is from https://p5js.org/reference/p5/floor/
    let nextColorIndex = (colorIndex + 1) % colors.length;

    // Blend between the two colors
    let blendAmount = (inter * (colors.length - 1)) - colorIndex;
    let c = lerpColor(colors[colorIndex], colors[nextColorIndex], blendAmount);
```
4. #### Dynamic Circle Ring Range Constraint：
By detecting the horizontal and vertical boundaries, the movement direction of the circle rings is reversed when they exceed the predefined range, ensuring they remain within the canvas.
```
 // Boundary for constraint (you can adjust 50 here to set buffer for the boundary)
     let boundary = -100;
     
     // Check horizontal boundaries
     if (this.x < -boundary || this.x > width + boundary) {
       this.velX *= -1; // Reverse horizontal direction
     }
     
     // Check vertical boundaries
     if (this.y < -boundary || this.y > height + boundary) {
       this.velY *= -1; // Reverse vertical direction
     }
```

5. #### Natural movement of meteors：
By applying Perlin noise to the meteor's velocity, subtle random variations are introduced, making the meteor's movement path more natural and smooth.
```
    this.noiseOffsetX = random(1000); // Offset for Perlin noise in the x direction
    this.noiseOffsetY = random(1000); // Offset for Perlin noise in the y direction
    this.noiseSpeed = 0.01; // Speed of Perlin noise change
```
```
 // Apply Perlin noise to introduce subtle random variations in velocity
    let noiseX = map(noise(this.noiseOffsetX), 0, 1, -1, 1); // Horizontal Perlin noise
    let noiseY = map(noise(this.noiseOffsetY), 0, 1, -1, 1); // Vertical Perlin noise
    
    // Adjust velocities slightly using Perlin noise while keeping the general direction
    this.vx = 4 + noiseX * 2; // Slight variation in the horizontal velocity
    this.vy = 4 + noiseY * 2; // Slight variation in the vertical velocity

    // Update the position of the meteor based on the velocities
    this.x += this.vx;
    this.y += this.vy;

    // Increment the noise offsets for the next frame
    this.noiseOffsetX += this.noiseSpeed;
    this.noiseOffsetY += this.noiseSpeed;“
```

### External Tools/Techniques：
- To achieve the gradient scrolling effect for the background color, I referred to the  **lerpColor()** function to blend the colors.This technique is from https://p5js.org/reference/p5/lerpColor/
```
   // Blend between the two colors
    let blendAmount = (inter * (colors.length - 1)) - colorIndex;
    let c = lerpColor(colors[colorIndex], colors[nextColorIndex], blendAmount);
```
- To achieve a smooth color gradient effect, I used the **floor()** function to generate integer indices, which are then used to select colors.
This technique is from https://p5js.org/reference/p5/floor/
```
  // Select two colors to blend based on position
    let colorIndex = floor(inter * (colors.length - 1));
    let nextColorIndex = (colorIndex + 1) % colors.length;

```
- To clean up meteor objects that move off the canvas, I used the **splice()** function to remove them from the array.
```
  for (let i = meteors.length - 1; i >= 0; i--) {
    // Clear the meteor when it reaches the bottom of the canvas.
    if (meteors[i].y > height / scaleFactor + 100)
      meteors.splice(i, 1); // This technique is from https://p5js.org/reference/p5/splice/
  }
```


