import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH = 400
SCREEN_HEIGHT = 600

# Colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)

# Create the screen
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Flappy Ninja Bird")

# Load images
background_image = pygame.image.load("background.png").convert()
bird_image = pygame.image.load("ninja_bird.png").convert()
bird_image.set_colorkey(BLACK)

# Bird properties
bird_x = 50
bird_y = 200
bird_y_change = 0

# Gravity and jump
gravity = 0.5
jump = -8

# Pipe properties
pipe_width = 70
pipe_height = random.randint(150, 400)
pipe_x = SCREEN_WIDTH
pipe_y = random.randint(150, 400)
pipe_speed = 4

# Game variables
score = 0
game_over = False

# Fonts
font = pygame.font.SysFont(None, 36)

def display_score(score):
    score_text = font.render("Score: " + str(score), True, WHITE)
    screen.blit(score_text, (10, 10))

def draw_bird(x, y):
    screen.blit(bird_image, (x, y))

def draw_pipe(x, y, height):
    pygame.draw.rect(screen, WHITE, [x, y, pipe_width, height])
    pygame.draw.rect(screen, WHITE, [x, y + height + 150, pipe_width, SCREEN_HEIGHT - (y + height + 150)])

def check_collision(bird_x, bird_y, pipe_x, pipe_y, pipe_height):
    if bird_x + 48 > pipe_x and bird_x < pipe_x + pipe_width:
        if bird_y < pipe_y or bird_y + 48 > pipe_y + pipe_height + 150:
            return True
    return False

# Game loop
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and not game_over:
                bird_y_change = jump

    if not game_over:
        screen.blit(background_image, (0, 0))

        # Bird
        bird_y_change += gravity
        bird_y += bird_y_change
        draw_bird(bird_x, bird_y)

        # Pipes
        pipe_x -= pipe_speed
        draw_pipe(pipe_x, pipe_y, pipe_height)

        # Collision
        if check_collision(bird_x, bird_y, pipe_x, pipe_y, pipe_height):
            game_over = True

        # Score
        if pipe_x < bird_x and pipe_x > bird_x - pipe_speed:
            score += 1

        # Check if pipe is off the screen
        if pipe_x < -pipe_width:
            pipe_x = SCREEN_WIDTH
            pipe_height = random.randint(150, 400)
            pipe_y = random.randint(150, 400)

        # Check if bird is off the screen
        if bird_y > SCREEN_HEIGHT or bird_y < 0:
            game_over = True

        display_score(score)

    else:
        game_over_text = font.render("Game Over", True, WHITE)
        screen.blit(game_over_text, (SCREEN_WIDTH // 2 - 100, SCREEN_HEIGHT // 2 - 20))

    pygame.display.update()
    clock.tick(60)

pygame.quit()

