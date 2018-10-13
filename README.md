# Snake_Pygame
Classic 'Snake' game with Pygame library

User starts as a single dot and has to work their way around the map collecting red dots (apples) for points.  Player loses if 
they run into their own tail.

![pic01](https://user-images.githubusercontent.com/34739163/46910089-beeb3980-cefb-11e8-8112-6d188591b5ab.png)

![pic02](https://user-images.githubusercontent.com/34739163/46910090-c01c6680-cefb-11e8-8073-86d4d011b8c9.png)

![pic03](https://user-images.githubusercontent.com/34739163/46910091-c14d9380-cefb-11e8-9de2-786ec502ae39.png)

Now that we have an idea of what we're making lets dive into the code!

1. Import pygame and needed libraries

```
# Classic 'Snake' game with Pygame library
import pygame
import sys
import random

from pygame.locals import *
```

2. Now for the details of our game. (window, movement grid, etc)

```
# Initiate our game and set FPS 
FPS = 15 # Can alter FPS to change speed and difficulty.
pygame.init()
fpsClock=pygame.time.Clock()

# Create our game window
SCREEN_WIDTH, SCREEN_HEIGHT = 640, 480
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT), 0, 32)
surface = pygame.Surface(screen.get_size())
surface = surface.convert()
surface.fill((255,255,255))
clock = pygame.time.Clock()

pygame.key.set_repeat(1, 40)

# Establish grid
GRIDSIZE=10
GRID_WIDTH = SCREEN_WIDTH / GRIDSIZE
GRID_HEIGHT = SCREEN_HEIGHT / GRIDSIZE

# Directional inputs for user
UP    = (0, -1)
DOWN  = (0, 1)
LEFT  = (-1, 0)
RIGHT = (1, 0)
    
screen.blit(surface, (0,0))
```
3. Now we create the 2 object classes of our game. The snake (player) and the apple (objective)

```# Create our snake object (player)
class Snake(object):
    def __init__(self):
        self.lose()
        self.color = (0,0,0) # Make snake body black

    # Return players position
    def get_head_position(self):
        return self.positions[0]

    # Resets game if you lose
    def lose(self):
        self.length = 1
        self.positions =  [((SCREEN_WIDTH / 2), (SCREEN_HEIGHT / 2))]
        self.direction = random.choice([UP, DOWN, LEFT, RIGHT])

    # Tracks score
    def point(self, pt):
        if self.length > 1 and (pt[0] * -1, pt[1] * -1) == self.direction:
            return
        else:
            self.direction = pt

    # Move logic for player
    def move(self):
        cur = self.positions[0]
        x, y = self.direction
        new = (((cur[0]+(x*GRIDSIZE)) % SCREEN_WIDTH), (cur[1]+(y*GRIDSIZE)) % SCREEN_HEIGHT)
        # Collision detection
        if len(self.positions) > 2 and new in self.positions[2:]:
            self.lose()
        else:
            self.positions.insert(0, new)
            if len(self.positions) > self.length:
                self.positions.pop()

    # Draws our snake
    def draw(self, surf):
        for p in self.positions:
            draw_box(surf, self.color, p)
         
  #----------------------------------------------------------------------
  
  # Create our apple object (player objective)
class Apple(object):
    def __init__(self):
        self.position = (0,0)
        self.color = (255,0,0) # Make our apple red
        self.randomize() # Randomize spawnpoint using our grid

    # Randomizes apple spawn
    def randomize(self):
        self.position = (random.randint(0, GRID_WIDTH-1) * GRIDSIZE, random.randint(0, GRID_HEIGHT-1) * GRIDSIZE)

    # Draws our apple
    def draw(self, surf):
        draw_box(surf, self.color, self.position)

# Takes in snake object and apple object to see if player scored
def check_eat(snake, apple):
    if snake.get_head_position() == apple.position:
        snake.length += 1
        apple.randomize()
```
Finally we run our game with call to main().

```
# Run our game
if __name__ == '__main__':
    snake = Snake()
    apple = Apple()
    while True:

        # USER INPUTS:
        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()
                sys.exit()
            elif event.type == KEYDOWN:
                if event.key == K_UP:
                    snake.point(UP)
                elif event.key == K_DOWN:
                    snake.point(DOWN)
                elif event.key == K_LEFT:
                    snake.point(LEFT)
                elif event.key == K_RIGHT:
                    snake.point(RIGHT)

        surface.fill((255,255,255)) # Make our background white
        snake.move()

        check_eat(snake, apple)
        snake.draw(surface)
        apple.draw(surface)

        font = pygame.font.Font(None, 36)
        text = font.render(str(snake.length), 1, (10, 10, 10))
        textpos = text.get_rect()
        textpos.centerx = 20

        surface.blit(text, textpos)
        screen.blit(surface, (0,0))

        pygame.display.flip()
        pygame.display.update()

        fpsClock.tick(FPS + snake.length/3)
```
