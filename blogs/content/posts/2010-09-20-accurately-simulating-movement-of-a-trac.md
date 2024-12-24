---
title: Accurately Simulating Movement of a Tracked Vehicle in 2D Space
author: Rob Earl
type: post
date: 2010-09-20T16:41:38+00:00
ID: 896
excerpt: "As part of a simulation I've been developing I recently had to decide how to model movement of vehicles within a 2D space. To keep things simple I settled on a tracked vehicle. Each time the simulation updates I calculate how much the vehicle rotates an&hellip;"
url: /index.php/webdev/uidevelopment/accurately-simulating-movement-of-a-trac/
views:
  - 13059
rp4wp_auto_linked:
  - 1
categories:
  - UI Development
tags:
  - 'c#'
  - java
  - math
  - simulation
  - tracking

---
<img align="right" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/robearl/TankMovement-Simplified.png" alt="Basic Tracked Movement" title="" />As part of a simulation I've been developing I recently had to decide how to model movement of vehicles within a 2D space. To keep things simple I settled on a tracked vehicle. Each time the simulation updates I calculate how much the vehicle rotates and how far it moves based on the speed of it's tracks. From there I calculate the new coordinates for redrawing the vehicle.

Knowing the current location (**x,y**) and heading (**h**) I initially adopted a simplified way to calculate the rotation (**g**) and speed (**s**) based on the speed of the vehicle's tracks (**T<span class="MT_smaller">1</span>** and **T<span class="MT_smaller">2</span>**) (Figure 1). With these variables I create a right angled triangle and use [maths][1] to calculate how far the vehicle moves in the x and y coordinates in order to find it's finishing location (**x',y'**).

```
x' = x + s * sin(h + g)
y' = y + s * cos(h + g)
```
This method is a reasonable approximation for the movement of a tracked vehicle: It'll move faster when you expect it to and it'll turn faster when you expect it to. However, as a [stand up mathematician][2] pointed out to me, it's not an accurate representation of what should actually be happening. I had also observed vehicles having problems turning to face something close to and behind them resulting in endless circling of the target.

# Improving Accuracy

A more accurate representation is the vehicle travelling a distance, **s**, along the circumference of a circle who's radius can be calculated based on **T<span class="MT_smaller">1</span>**, **T<span class="MT_smaller">2</span>** and the distance between the tracks (**2d**) (Figure 2).

<img align="right" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/robearl/TankMovement-Complex-bare3.png" alt="Figure 2: More Accurate Movement" title="" />If **T<span class="MT_smaller">2</span>** traces a circle of radius **r** then **T<span class="MT_smaller">1</span>** traces a circle of radius **r + 2d**. Since **d** is static we can say that the ratio of **T<span class="MT_smaller">1</span>** to **T<span class="MT_smaller">2</span>** is proportional to the ratio of the radii. This gives us the following equation to solve:

```
T1   r + 2d
-- = -------
T2     r
```
Solving this for r gives us:

```
r =     2d 
    ----------
     T1
     --  -  1
     T2
```
## Rotation

We now have a triangle with 2 sides of known length. If we divide this triangle in half we can use the right angles to determine the vehicle will move **2g** radians around the circle (Figure 3).

We can say that the ratio between **2g** and 2 PI radians (360 degrees) is the same as the ratio between **s** and the circumference of the circle (2 \* PI \* (**r** + **d**)). Setting them to equal we can solve for **g**.


<img align="right" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/robearl/TankMovement-Complex-Solving.png" alt="Figure 3: Solving Triangles" title="" /> 

```
2g                s
------  =  -----------------
2 * PI      2 * PI * (r + d)
       s
2g = -------
     r + d

2g = T1 + T2
    --------
       2
    --------
     r + d

2g =    T1 + T2
     --------------
      2 * ( r + d )

g  =     T1 + T2
      -------------
      4 * ( r + d )
```
If we substitute in our value for **r** and simplify we end up with:

```
g = T1 - T2
    --------
       4d
```
Which is a fairly simple calculation that scales by the size of the organism. The other thing missing from the approximate calculation is that the change in heading is actually **2g**, as can be seen from the symmetry in our diagrams.

## Distance

Now that we know the angles we can use the [Law of Sines][3] to calculate the straight line distance, **t**, between our vehicle's start and end points. 

```
r + d        t / 2
--------- = -------
sin(PI/2)    sin(g)

r + d     t / 2
-----  = -------
  1       sin(g)

sin(g) * (r + d) = t / 2

t = 2 * sin(g) * (r + d)
```
Once again we can substitute in our value for **r** and simplify to get:

```
t = d(T1 + T2)
    ---------- * 2 * sin(g)
      T1 - T2
```
Now that we know the distance and we know the angle we can use the same method we used earlier to calculate the new coordinates:

```
x' = x + t * sin(h + g)
y' = y + t * cos(h + g)
```
## Applying Our Findings

So far we've seen a lot of (possibly confusing) numbers and symbols but what do we actually have to do do add this to a simulation? Each time our vehicle updates we need to:

  1. Calculate **g**.
  2. Calculate **t**.
  3. Calculate **x'**.
  4. Calculate **y'**.
  5. Adjust the vehicle's coordinates to **x',y'**.
  6. Adjust the vehicle's heading (**h**) by **2g**.

You may have noticed there's a possible division by zero error when **T<span class="MT_smaller">1</span>** equals **T<span class="MT_smaller">2</span>**. To protect against this we can check if they're equal (or catch the exception) and use the calculation:

```
x' = x + T1 * sin(h)
y' = y + T1 * cos(h)
```
## Example

Let's say our vehicle has the properties:

T<span class="MT_smaller">1</span> = 1.0 (Track 1 Speed)
  
T<span class="MT_smaller">2</span> = 0.5 (Track 2 Speed)
  
d = 2.0 (Half distance between tracks)
  
x = 0 (Coordinate)
  
y = 0 (Coordinate)
  
h = 2 (Heading)

1. Calculate **g**

```
g = T1 - T2
    --------
       4d
g = 1.0 - 0.5     0.5
    ---------  =  --- = 0.0625
        8          8
```
2. Calculate **t**

```
t = d(T1 + T2)
    ---------- * 2 * sin(g)
      T1 - T2
= 0 + (2 * (1.0 + 0.5) * 2 * sin(0.0625) ) / ( 1.0 - 0.5 )
= (6 * 0.06246 ) / 0.5
= 0.37476 * 2
= 0.74951
```
3. Calculate **x'**.

```
x' = x + t * sin(h + g)
= 0 +  0.74951 * sin(2.0625)
= 0.66072

```
4. Calculate **y'**.

```
y' = y + t * cos(h + g)
= 0 + 0.74951 * cos(2.0625)
= -0.35387
```
2. Adjust **h** by **2g**.

```
h' = h + 2g
h' = 2 + 2 * 0.0625
h' = 2.125
```
After this update our new state is:

x = 0.66072
  
y = -0.35387
  
h = 2.125

In the following Java example the Vehicle's update method would be called from the main program loop.

```java
public class Vehicle
{
  private double x, y, heading;
  private int size, maxSpeed;
  /*
     Other methods for initialising and controlling the vehicle.
  */
  public void update()
  {
    double tracks[2] = controller.getTrackSpeeds(); // Get the track speeds from whatever class is controlling the vehicle.
                                                    // Could be human input or any kind of AI controller.
    // Calculate g.
    double d = this.size / 2;
    double g = (tracks[0] - tracks[1]) / ( d * 4);

    // Calculate t.
    double t = 0;
    if(tracks[0].equals(tracks[1])) // Use straight line calculation to avoid division by zero.
    {
      t = tracks[0];
    } 
    else
    {
      t = ( d * ( tracks[0] + tracks[1] ) * 2 * Math.sin(g) ) / ( tracks[0] - tracks[1] );
    }

    // Update x.
    this.x -= t * Math.sin(this.heading + g); // See note below.
                                              
    // Update y.
    this.y += t * Math.cos(this.heading + g);

    // Update the vehicle's heading.
    this.heading += 2 * g;
  }
}
```
When updating the x coordinate we subtract because, when using a JPanel:

  * 0,0 is the top left corner.
  * A heading of 0 is down.

Using a normal [sine wave][4] an increase in heading (**h**) would result in an increase in **sine h** and therefore, an increase in **x** which is the opposite of what is expected/required. 

Inverting the wave ( multiply by -1 ) produces the correct behaviour.

# How Much Difference Does It Really Make?

The accuracy of the basic method decreases as the section of the circle traversed in one increment moves further from a straight line. Therefore, it decreases as:


<img align="right" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/robearl/Charts.png" alt="Comparison Charts" title="" /> 

  * Speed increases.
  * Turning circle decreases.

To demonstrate just how much difference there can be I've used a fairly extreme case to produce the charts opposite:

T<span class="MT_smaller">1</span> = 10
  
T<span class="MT_smaller">2</span> = -9
  
d = 2

As we can see a vehicle turning in a tight circle at a fairly high speed has a much bigger turning circle using the approximate movement calculations than when using the improved calculations.

As the size of the vehicle is now taken into account we can make a vehicle less nimble with a larger turning circle simply by increasing it's size. We can get a turning circle like the one below by increasing **d** to 60 without any need to introduce extra variables to clamp the rotation rate.

<center>
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/robearl/Bigger.png" alt="Larger Vehicle" title="" />
</center>

The other difference which is fairly hard to quantify is that the vehicle movement was much smoother once the new calculations were implemented. This could be because the initial technique caused lots of over-rotating which required constant readjustment.

 [1]: http://www.mathsisfun.com/sine-cosine-tangent.html
 [2]: http://yourdaysarenumbered.co.uk/
 [3]: http://en.wikipedia.org/wiki/Law_of_sines
 [4]: http://en.wikipedia.org/wiki/Sine_wave