---
title: "Dhoom 2: A robbery car project"
tags: ["project"]
date: 17 Jan 2026
---


If you’re a millennial kid who grew up loving the first Dhoom movie. You know firsthand how grand the experience was to watch Dhoom 2. The high-tech gadgets, the famous diamond robbery - it was one of the best Bollywood sci-fi tech movies of its time.  I was just 10 years old back then.

Fast forward to 2026. Twenty years later, I think it’s finally time to build the car replica, to relive the nostalgia and have some fun.

We’ve all seen the craze around Iron Man suits and the 90s trend of Batmobile builds among young engineers. Movie-inspired projects have become a way to turn childhood inspiration into real-world creations.This is my take on bringing a bit of that movie magic to life.

So, let’s begin. _Cue_ _“**ta na na na na na na na na**”_

<br>

## First steps

I’ve been working on this project for a year now. It seemed simple at first, but once you get down to the details, things become far more trickier. This is why plan first, define the scope as early as possible.

In the movie, most of the props are created using a mix of CGI and practical effects, so it’s important to figure out what can be built practically. In the case of the _Dhoom 2_ car, I found that recreating the car’s design, adding a camera, and implementing the iris-opening mechanism are all doable. 

However, there are a few limitations. Features like the arm popping out (even though vacuum robots come with arms now[1]) and the car climbing over the diamond podium aren’t realistically achievable for this build. They're CG in the movie anyway.

When planning a project like this, you need to decide what you really want from it. Is it mainly about getting the look right, or do you also want real mechanical and electronic functionality? With movie props, appearance alone already takes up most of the effort. Matching what was shown on screen is easily the major challenge. Once that direction is clear, you can optimize the rest of the build around it.

So, with all that in mind, I decided to build following things since they're enough to film a sequence shown in the movie.


1. The Car
2. The Book
3. Staging the robbery scene

<br>

## The Car

In the movie, the car prop is used in the famous diamond robbery scene where the thief uses an RC car to steal a diamond from the museum. [Here's the link to refresh your memory](https://www.youtube.com/watch?v=3bMYgo_S0Kc)

The car’s design is surprisingly practical. Apart from the obvious CGI elements, most of its features felt plausible enough to attempt recreating practically.While reading about the VFX work on the movie, I found that Tata Elxsi was responsible for the car’s visual effects[2].

#### Build

First task is to estimate the dimensions of the car. Its tricky to do that because the perceived dimension will change based on the camera angle and distance.

{{< slideshow "600px" "300px" "film_compare2.jpg" "car_diff_size.jpg" "top_view.png">}}

Luckily, In one of the scene, they show the 35mm film beside the opened car. Since we know tts a 35mm film, we can measure how many pixels are the film and based on that guess the car dims. Its not accurate but enough to give us the ball park.  I did this in fusion 360 by importing image as canvas. This can also be done using OpenCV.

The actual dimensions came out to be 147mm×190mm. However, I wanted some buffer space on the print bed so I decided to somewhat scale the model down. 

{{< slideshow "600px" "300px" "image_calibrate.png" "car_actual_dimensions.png" "car_model_dims.png">}}

<br>
I've tried to replicate the design as much as possible.
{{< image "top_car.png" "car top view">}}

{{<note>}}

As you may have already noticed, there’s no iris-opening mechanism present. That’s the part I’m currently working on.
I skipped it for now, since this is more of a prototype v1.
{{</note>}}

Some of the internal structure shown earlier were't practical so I omitted them. The body is divided into two parts:
* The bottom section, which contains a cavity for the electronics.
* The top section, which holds the decorative wheels.
At the front, there’s a cut-out for the pinhole camera.

One interesting detail I noticed was the wheel design. In the movie, the wheels appear to be fixed on their spinning axis and don’t move left or right, yet the car still makes sharp turns. There are also ten visible wheels spinning, even though they don’t seem to steer. How they achieved this in the film is still a mystery to me.

{{< image "wheels_view.jpg" "car wheels" >}}

To tackle it from the mechanical perspective, I decided to attach motors to two front wheels.
By spinning them in opposite directions, the car can turn left or right. The drawback is that the remaining wheels tend to drag slightly during turns.

The wheels themselves are simple pulley designs modelled using an [OpenSCAD Python script](https://www.thingiverse.com/thing:4932243). I experimented with the parameters until the proportions looked right, then imported the final model into Fusion 360.


#### Electronics

The electronics setup is fairly simple. I’m using an ESP32-CAM for the live video feed. It has enough GPIO pins to control the motor driver and can run on a relatively small LiPo battery. Since the build only uses two geared N20 motors, I went with the compact DRV8833 motor driver. I also had to use an extension ribbon cable and an adapter for the camera module.

{{< image "car_electronics.jpg" "car elex" >}}

The motors are 12V N20 geared motors rated at 60 RPM. They’re slow, but they provide enough torque to carry the full weight of the car. The battery is a 7.4V, 600mAh two-cell LiPo, which is the only one that could fit inside the bottom cavity. A step-down (buck) module is required to safely power the ESP32.


#### Software

High level architecture:
{{< image "esp32_sw.png" "sw" >}}

On the ESP32, I’m using the ESP-IDF framework. Separate tasks are created for the UDP/HTTP handling and the camera.
The MJPEG stream uses GET requests to send image frames. To read joystick data from the main controller, I use both UDP and HTTP GET requests, depending on the setup.

All in all, I’ve tried to achieve a look similar to what’s shown in the movie.

<br>

## The Book
The book is another important piece of the puzzle. In the movie, the antagonist uses the book as a
decoy to wirelessly operate the RC car.

{{< image "the_book.jpg" "og book" >}}


#### Build

Unlike the car, the book is fairly practical to build. The book contains a joystick and a few additional
buttons to control the car. The camera mounted on the car sends a live video feed to the screen embedded inside the book.

{{< slideshow "600px" "300px" "book_screen.jpg" "book_liveFeed.jpg" "book_dims.png">}}


I used an online service to 3D print the book because I couldn’t achieve the same color and finish with my printer.
The buttons and joystick were printed with and painted as well . I still managed to make a few mistakes.
The top cover’s binding isn’t as curved as the bottom half, and in the movie,
the bottom section is slightly larger, something I forgot to account for. Still, I think it turned out pretty close.

#### Electronics

The high level design of the book's electronics is shown here:

{{< image "milkv_hw.png" "hw" >}}


And the electronics arranged inside the book:

{{< image "books_inside.jpg" "hw" >}}

The Milk-V Duo S SBC is the central controller of the system. It handles all the thread logic, reads user input, sends data to the ESP32, and controls the display. The board is powered by a 5000mAh power bank.

{{<note>}}

In hindsight, many things on both the software and hardware sides could have been simpler if I had used a Raspberry Pi Zero W. However, I was already testing the Milk-V board, so I decided to go ahead with it.
{{</note>}}

The buttons and joystick are connected to a PCF8574A I²C I/O expander instead of directly to the main board, since most of the 3.3V header pins are already used for the SPI interface.

The LCD is connected to the Milk-V Duo S via SPI and uses the ILI9488 driver. It is used to display the live video stream.


#### Software
A high-level overview of the software architecture is shown below:

{{< image "milkv_sw_arch.png" "sw" >}}

The libcurl library fetches the MJPEG stream from the ESP32 using GET requests, parses the buffer, and once a valid frame is found, pushes it onto a queue. From there, the writer thread saves the raw MJPEG data to disk. 
In parallel, the display thread sends the frames to the screen. This is the car’s pov camera feed.

{{<note>}}
The SPI interface sends the _entire frame_ to the framebuffer. There is no partial framebuffer update, which makes the display refresh quite slow. Also, I’m using opencv-mobile, a stripped-down version of OpenCV that lacks MP4/H.264 container support, so I had to write the raw frames directly to disk.
{{</note>}}

The GPIO thread reads input data over I²C every 10 ms using the WiringX library. Each button press is registered only once and then locked, while the joystick is continuously polled for new input. The `gpio_status_relay_thread` then parses these values, builds a GET URL with the parameters, and sends it to the ESP32 to control the car’s movement.

Now, let’s take a closer look at how the buttons work. There are four buttons on the book. As shown in the movie scene, each button performs multiple actions depending on what the car is doing at that moment (more on this later). The exact sequence will be explained in the #robberyscene. However, the mapping is:

* _RED BUTTON_          :Pressed once at the start. Car moves a certain distance.
* _RETURN BUTTON_       :Start showing the live feed
* _SAVE BUTTON_         :Opens car up for the climbing
* _HOME BUTTON_         :Start showing the static noise.

Another interesting aspect of this setup is the visual effects. In the movie, the book’s live feed has several VFX layers added on top. I wanted to recreate these as closely as possible.

* The grid is a lens-distorted PNG overlay
* The static effect is a `.dat` file containing raw noise frames, played before the live stream starts
* The green tint and flicker are added dynamically to each frame

All of these effects are composited on top of the frame based on the button states.

{{< slideshow "600px" "300px" "replica_pov_effects.jpg" "actual_pov_effects.png">}}

{{<note>}}
Random text overlays are still a work in progress.
{{</note>}}

Overall, the software ties the book’s controls, visual effects, and car communication into a single system.
<br>

## The Robbery scene

For the black strip, I used black plastic tape on the floor. I arranged each piece so the car could show off its turning capability while blending into the strip’s dark color. And for the diamond, I initially forgot about building the podium and the holder.
I ordered the diamond from Amazon and later 3D printed a holder for it. It’s not perfect, but it gets the job done.

With the setup finished and the system ready, it’s time to bring the robbery scene to life on camera:

```
The car is hidden out of sight, with a black strip guiding the path toward the diamond. 
The book controller is powered on, and everything is in position to begin the robbery sequence.

* Theif presses the RED button.
	- The car rolls out from its hiding place.
	- It crosses the exposed white floor.
	- Then it stops on the main black strip, facing the correct direction.

* Theif then presses HOME button.
	- The book’s display switches to static noise,
    just like in the movie when the lens cover retracts.

* Theif then presses RETURN button.
	- The live feed from the car’s POV camera appears on the screen.

* Theif uses joytsick to drive the car.
	- Keeping it aligned with the black strip, I guide it closer to the diamond.

* What happens next?
	- To be continued
```

For now, this is where the scene ends. I hope to add the iris mechanism and extend it further in the future.

{{<note>}}
All files are available at [github](https://github.com/Adirockzz95/Dhoom2Car)
{{</note>}}

Thank you for reading through this project.


### References
1. [vacuum robot arm](https://www.youtube.com/watch?v=X7wwVwW-SCY)
2. [Dhoom 2 animationxpress](https://www.animationxpress.com/vfx/vfx-takes-centrestage-in-bollywood-with-yashraj-films-dhoom-2/)