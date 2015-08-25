# The-Snake-Game
__author__ = 'WeiYang'


import pygame
from random import randint

# Basic colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
GREEN = (0, 255, 0)
RED = (255, 0, 0)


pygame.init()

size = (width, height) = (400, 400)
screen = pygame.display.set_mode(size)

pygame.display.set_caption("Snake")

clock = pygame.time.Clock()

running = True
on_initialization = True
food_check_condition = True

# ______________________________________________________________________________________________________________


def snake_init():
    # Finds the center of the map and starts the first list coordinate.
    snake_seed_x = width/2
    snake_seed_y = height/2
    snake.append([snake_seed_x, snake_seed_y])


def snake_key_movement(speed, velocity):
    # When key is pressed. It checks the correct keys and make sure it does not goes backwards.
    if event.type == pygame.KEYDOWN:
        if event.key == pygame.K_LEFT and velocity[0] != speed:
            velocity = (-speed, 0)
            print('Left arrow pressed')
        if event.key == pygame.K_RIGHT and velocity[0] != -speed:
            velocity = (speed, 0)
            print('Right arrow pressed')
        if event.key == pygame.K_UP and velocity[1] != speed:
            velocity = (0, -speed)
            print('Up arrow pressed')
        if event.key == pygame.K_DOWN and velocity[1] != -speed:
            velocity = (0, speed)
            print('Down arrow pressed')
    return velocity


def new_snake_position(position, velocity):
    next_snake_position = [position[-1][0] + velocity[0], position[-1][1] + velocity[1]]
    snake.append(next_snake_position)
    return snake


def out_of_bound_check(snake):
    if snake[-1][0] >= width or snake[-1][0] <= 0:
        print('You hit the wall. GG')
        return False
    elif snake[-1][1] >= width or snake[-1][1] <= 0:
        print('You hit the wall. GG')
        return False
    else:
        return True


def self_bumped(snake):
    for x in range(0, len(snake)-1):
        print(x)
        print(len(snake)-1)
        print(range(0,len(snake)-1))
        if snake[x][0] == snake[-1][0] and snake[x][1] == snake[-1][1]:
            print('You ate yourself!')
            return False
            break
        else:
            return True


def conflicting_position_food_check(food_position):
    # Checks that the food coordinate is not in the same position as the snake
    for coordinates in snake:
        if food_position == coordinates:  # If food occupies same space as the snake, breaks loop.
            return True
            print('Recalculating food position')
            break
        else:
            return False


def existing_food_check(food_position):
    if food_position == []:
        print('There is no food on the screen. Spawning new food.')
        return True
    else:
        return False


def spawn_food(food_position):
    food_check_condition = existing_food_check(food_position)

    while food_check_condition:
        # Generates a random coordinate on the screen
        print('generating new food location')
        food_coor_x = randint(0, (width/20)-1)
        food_coor_y = randint(0, (height/20)-1)
        food_position = [food_coor_x*20, food_coor_y*20]
        # Checks whether the coordinates is occupying the same position as the snake.
        food_check_condition = conflicting_position_food_check(food_position)
    # Spawns food on the food position
    return food_position


def eats_food(snake, food_position):
    if snake[-1][0] == food_position[0] and snake[-1][1] == food_position[1]:
        print('Snake has grown')
        food_position.pop(0)
        food_position.pop(0)
    else:
        snake.pop(0)


# ______________________________________________________________________________________________________________

while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Clears the entire screen
    screen.fill(WHITE)

    # Initialization: Set the snake position to the middle of the screen and sets the initial velocity.
    # Spawns initial food - Only if there is no food present on the screen.
    while on_initialization:
        # The dictionary [snake] stores each snake position.
        snake = []
        snake_init()
        # Velocity of the snake/ Discretized the screen into individual boxes
        speed = 20
        # Contains both x and y velocity
        snake_velocity = (-speed, 0)
        # Spawns food
        # food_position = spawn_food
        food_position = [100, 200]
        on_initialization = False

    # Calculates the new velocity (speed and direction) of the snake given a keyboard input
    snake_velocity = snake_key_movement(speed, snake_velocity)

    # Calculates the new position with the new velocity and stores it in [snake].
    # Still keeps the previous snake position within [snake]
    new_snake_position(snake, snake_velocity)

    # Checks if the snake is out of bounds or collides with itself.
    # Stops the program if collision occurs.
    if out_of_bound_check(snake) and self_bumped(snake):
        running = True
    else:
        running = False

    # Checks if the snake collides with the food.
    # If so, the previous position of the snake is kept and the food is removed.
    # Else, it removes the previous position of the snake, maintaining the same snake length
    eats_food(snake, food_position)

    # Checks if there is any food on the map, else spawns a new food on a random location.
    food_position = spawn_food(food_position)

    # Draws each individual chain of snake by retrieving all its position.
    for n in range(0, len(snake)):
        pygame.draw.rect(screen, RED, [snake[n][0], snake[n][1], 20, 20], 2)
    # Draws the food on the screen.
    pygame.draw.rect(screen, GREEN, [food_position[0], food_position[1], 20, 20], 2)

    # Updates the screen.
    pygame.display.flip()
    clock.tick(5)

pygame.quit()
