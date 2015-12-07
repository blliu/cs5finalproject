#
# Names: Priscilla Chu and Briana Liu
#

from visual import *
import random

def make_walls():
    """makes several walls and returns them in a list
    """
    w0 = box(pos=(-20,0,0), axis=(0,0,1), 
             length=40, width=1, height = 2, color=color.red)
    w1 = box(pos=(0,0,-20), axis=(1,0,0), 
             length=40, width=1, height = 2, color=color.orange)
    w2 = box(pos=(0,0,20), axis=(1,0,0), 
             length=40, width=1, height = 2, color=color.yellow)
    w3 = box(pos=(20,0,0), axis=(0,0,1), 
             length=40, width=1, height = 2, color=color.green)
    list_of_walls = [ w0, w1, w2, w3 ]
    return list_of_walls

def make_maze():
    """makes walls that stick out of the walls to make a maze
    """
    m0 = box(pos=(-9,0,-7), axis=(1,0,0), 
             length=25, width=3, height = 2, color=color.blue)
    m1 = box(pos=(0,0,-20), axis=(1,0,0), 
             length=20, width=1, height = 2, color=color.blue)
    m2 = box(pos=(0,0,20), axis=(1,0,0), 
             length=20, width=1, height = 2, color=color.blue)
    m3 = box(pos=(9,0,7), axis=(1,0,0), 
             length=25, width=3, height = 2, color=color.blue)
    list_of_maze = [ m0, m1, m2, m3 ]
    return list_of_maze

def make_sheep():
    """ makes a sheep!
    """
    sheep = frame( pos=(10,0,-10) )  # makes a new "frame" == a container
    # with a local coordinate system that can have any number of parts...
    # here are the "parts":
    # doesn't show up.. sphere(frame=sheep, radius =0.4, pos =(-.4,-.2,-.4), color=color.black)
    sphere( frame=sheep, radius=1, color=color.white )
    sphere( frame=sheep, radius=0.6, pos =(.5,.5,1), color=color.black )
    sphere( frame=sheep, radius=0.3, pos =(-.5,.5,-.5), color=color.black )


    return sheep

def make_flower():
    """ makes a flower """
    # color=(1,0.7,0.2)
    flower1 = frame( pos=(9,0,-9))
    ellipsoid(frame=flower1, pos=(0,0,0), 
          length=3, height=1, width=1, color = color.magenta)
    ellipsoid(frame=flower1, pos=(0,0,0), 
          length=1, height=1, width=3, color = color.magenta)

    return flower1

def make_flower2():
    """ makes a flower """
    flower2 = frame( pos=(7,0,-7))
    ellipsoid(frame=flower2, pos=(0,0,0), 
          length=3, height=1, width=1, color = color.yellow)
    ellipsoid(frame=flower2, pos=(0,0,0), 
          length=1, height=1, width=3, color = color.yellow)

    return flower2


def main():
    """ this is the main function, including
        all of the data objects and the "event loop"
        which is the while True: loop that will
        be the universe's "time stream" :-)
    """
    # create an object named floor of class (type) box:
    floor = box(pos=(0,-1,0), length=40, width=40, height = 0.5, color=color.white)

    # this creates a list of walls 
    Walls = make_walls()
    w0, w1, w2, w3 = Walls   # and gives each one a name...

    Maze = make_maze()
    m0, m1, m2, m3 = Maze

    wolf = sphere( radius=1, pos=(0,0,0), color=(1,0.7,0.2) )
    wolf.vel = vector(0,0,0)   # this is the "game piece" w/ zero starting vel.
    
    sheep = make_sheep()
    sheep.vel = vector(0,0,0)  # no velocity (yet!)

    flower1 = make_flower()
    flower2 = make_flower2()
    flower1.vel = vector(0,0,0)
    flower2.vel = vector(0,0,0)

    # color names? they are black, blue, cyan, green, gray(v), 0.0<=v<=1.0
    #                       magenta, orange, red, white, yellow
    # or you can use rgb tuples (from 0.0 to 1.0, not 0 to 255), e.g.,
    
    # We set some variables to control the display and the event loop
    RATE = 30             # number of loops per second to run, if possible!
    dt = 1.0/(1.0*RATE)   # the amount of time per loop (again, if possible)
    

    # this is the main loop of the program! it's "time" or the "event loop"
    while True:
        rate(RATE)     # run no faster than RATE loops/second
        autocenter = False    # do you want vPython to keep the scene centered?
        # +++++ start of all position updates: once per loop +++++ 

        flower1.pos = flower1.pos + flower1.vel*dt           # PHYSICS!
        flower2.pos = flower2.pos + flower2.vel*dt
        sheep.pos = sheep.pos + sheep.vel*dt
   

        # +++++ end of all once-per-loop position updates +++++ 

        # ----- start of other checks - especially *collisions* -----

        # collision check for the wolf(ves) and sheep...
        # vector docs:   http://vpython.org/contents/docs/vector.html
        vec_from_wolf_to_sheep = wolf.pos - sheep.pos
        if mag( vec_from_wolf_to_sheep ) < wolf.radius:
            print "You've killed the sheep! *floats to sheep heaven*..."
            sheep.vel = vector(0,5,0)
    
        # collision check for the sheep and flower(s)...
        # vector docs:   http://vpython.org/contents/docs/vector.html
        vec_from_sheep_to_flower1 = flower1.pos - sheep.pos
        if mag( vec_from_sheep_to_flower1 ) < 1:
            print "Flowers are yummy!!"
            flower1.vel = vector(0,5,0)

        vec_from_sheep_to_flower2 = flower2.pos - sheep.pos
        if mag( vec_from_sheep_to_flower2 ) < 1:
            print "Flowers are yummy!!"
            flower2.vel = vector(0,5,0)


        
        # need to handle wall collisions next...
        # here is an example of one wall collision - you'll need more!
        
        # colliding with wall 0, w0:
        if sheep.pos.x < w0.pos.x:  # w0 has the smallest x value
            sheep.pos.x = w0.pos.x  # make sure we stay in bounds
            sheep.vel.x = -1.0 * sheep.vel.x   # bounce (in the x direction)
        if sheep.pos.z < w1.pos.z:
            sheep.pos.z = w1.pos.z
            sheep.vel.z = -1.0 * sheep.vel.z
        if sheep.pos.z > w2.pos.z:  
            sheep.pos.z = w2.pos.z  
            sheep.vel.z = -1.0 * sheep.vel.z  
        if sheep.pos.x > w3.pos.x:
            sheep.pos.x = w3.pos.x
            sheep.vel.x = -1.0 * sheep.vel.x



        # ----- end of other checks - especially *collisions*  -----




        # ===== handling user events: keypresses and mouse =====

        # here, we see if the user has pressed any keys
        if scene.kb.keys:   # any keypress to be handled?
            s = scene.kb.getkey()
            print "You pressed the key", s  

            # Key presses to give the ball velocity (in the x-z plane)
            dx = .5; dz = .5   # easily-changeable values
            if s == 'left': sheep.vel += vector(-dx,0,0)
            if s == 'right': sheep.vel += vector(dx,0,0)
            if s == 'up': sheep.vel += vector(0,0,-dz)
            if s == 'down': sheep.vel += vector(0,0,dz)

            # space to stop everything
            if s == ' ':  # space to stop things
                flower1.vel = vector(0,0,0)
                flower2.vel = vector(0,0,0)
                sheep.vel = vector(0,0,0)
                wolf.vel = vector(0,0,0)

            # capital R to reset things
            if s == 'R':
                sheep.vel = vector(0,0,0)
                sheep.pos = vector(0,0,0)
                flower1.vel = vector(0,0,0)
                flower1.pos = vector(-10,0,random.uniform(-10,10))
                wolf.vel = vector(0,0,0)
                wolf.pos = vector(0,0,0)
    
    #GRUTOR MODE (makes wolves disappear!!)
    #if s == 'G':
#    make_walls()
    
            if s == 'Q':  # Quit!
                print "Quitting..."
                break  # breaks out of the main loop

            # mouse is better handled only when a particular key is pressed
            # see http://vpython.org/contents/docs/mouse.html for more

        # ===== end of handling user events: keypresses and mouse =====

    print "Done with the main loop. Ending this vPython session..."
    print "Close the vPython window to finish."
# this ends the main() function - it tends to get large!


# This should be the FINAL thing in the file...
if __name__ == "__main__":   # did we just RUN this file?
    main()                   # if so, we call main()
