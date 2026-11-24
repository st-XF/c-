# ============================================================
# ELEC 3A - Graphics and Visual Computing
# Laboratory Activity 04: Object-Oriented Interactive Graphics
# and 2D Animation
# ============================================================
# Laboratory Procedures
#
# Part 1. Create the Project
# 1. Open your VS Code.
# 2. Create a new Python file named Lab4_<LastName>.py.
# 3. Import the required libraries.
# 4. Initialize Pygame and create a window with a resolution
#    of 900 x 600 pixels.
# Your task:
# - Run the program.
# - Verify that the application window appears.
# - Set the background color to white.
# - Ensure that the application runs at 60 frames per second.
#
# Part 2. Apply Object-Oriented Programming
# Create a class that represents a graphical object.
# Step 1. Create the GraphicObject class.
# Step 2. Add a drawing method named draw().
# Your task:
# 1. Create three objects using the GraphicObject class.
# 2. Assign each object a different shape.
# 3. Give each object a different color.
# 4. Position the objects in different areas of the window.
#
# Part 3. Implement Animation Principles
# Step 1. Add animation attributes.
# Step 2. Create an animation method.
# Step 3. Apply the animation method.
# Step 4. Add animation controls.
# Press the SPACEBAR to start or stop the animation.
#
# Part 4. Apply the Principles of Animation
# 1. Squash and Stretch
# 2. Anticipation
# 3. Slow In and Slow Out
# 4. Follow-Through
# 5. Timing and Spacing
# Your task:
# - Squash and stretch when the object hits the ground.
# - Anticipation before the object moves upward.
# - Different animation speeds for at least two objects.
# - A follow-through or settling movement after landing.
#
# Part 5. Integrate Keyboard and Mouse Interaction
# Implement the following controls:
# Keyboard / Mouse Input | Function
# 1                      | Select the triangle
# 2                      | Select the rectangle
# 3                      | Select the circle
# Arrow keys             | Move the selected object
# + / -                  | Increase or decrease object size
# R                      | Rotate the selected object
# C                      | Change the selected object's color
# SPACEBAR               | Start or stop animation
# Mouse click            | Select an object
# Mouse drag             | Move the selected object
# ESC                    | Exit the application
# Your task:
# 1. Implement object selection using keyboard input.
# 2. Implement mouse-based selection and dragging.
# 3. Allow the selected object to move using the arrow keys.
# 4. Allow users to resize and change the color of an object.
# 5. Ensure that the animation continues to function correctly
#    when an object is selected or moved.
#
# Application Requirements
# - Object-oriented structure: Uses at least one class
# - Graphical elements: triangle, rectangle, and circle
# - Object attributes: position, shape, color, and size
# - Object methods: drawing and manipulating objects
# - Animation: At least two objects demonstrate animation
# - Animation principles: At least four principles
# - User interaction: keyboard and mouse interaction
# - Animation control: start and stop animation
# - Visual presentation: clean and organized interface
# ============================================================

import pygame
import math
import sys

pygame.init()
WIDTH = 900
HEIGHT = 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Interactive Graphics and Animation Studio")
clock = pygame.time.Clock()
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 100, 255)
GREEN = (0, 200, 100)
FPS = 60

class GraphicObject:
    def __init__(self, x, y, shape, color, size=50):
        self.x = x
        self.y = y
        self.shape = shape
        self.color = color
        self.size = size
        self.angle = 0
        self.selected = False
        self.velocity_y = 0
        self.gravity = 0.5
        self.bounce_strength = -10
        self.is_animating = False
        self.scale_x = 1.0
        self.scale_y = 1.0

    def draw(self, surface):
        if self.shape == "circle":
            pygame.draw.circle(
                surface,
                self.color,
                (int(self.x), int(self.y)),
                self.size
            )
        elif self.shape == "rectangle":
            rect = pygame.Rect(
                self.x - self.size,
                self.y - self.size,
                self.size * 2,
                self.size * 2
            )
            pygame.draw.rect(surface, self.color, rect)
        elif self.shape == "triangle":
            points = [
                (self.x, self.y - self.size),
                (self.x - self.size, self.y + self.size),
                (self.x + self.size, self.y + self.size)
            ]
            pygame.draw.polygon(surface, self.color, points)

        if self.selected:
            pygame.draw.circle(surface, BLACK, (int(self.x), int(self.y)), self.size + 10, 2)

    def animate(self):
        if not self.is_animating:
            return
        self.velocity_y += self.gravity
        self.y += self.velocity_y
        if self.y + self.size >= HEIGHT - 50:
            self.y = HEIGHT - 50 - self.size
            self.velocity_y = self.bounce_strength
            self.scale_x = 1.2
            self.scale_y = 0.8
        else:
            self.scale_x = 1.0
            self.scale_y = 1.0

objects = [
    GraphicObject(200, 250, "triangle", RED, 50),
    GraphicObject(450, 250, "rectangle", BLUE, 50),
    GraphicObject(700, 250, "circle", GREEN, 50)
]

objects[0].gravity = 0.5
objects[1].gravity = 0.7
objects[2].gravity = 0.4
objects[0].bounce_strength = -10
objects[1].bounce_strength = -12
objects[2].bounce_strength = -9

selected_index = 0
objects[selected_index].selected = True
dragging = False

def select_object(index):
    global selected_index
    for obj in objects:
        obj.selected = False
    selected_index = index
    objects[selected_index].selected = True

colors = [RED, BLUE, GREEN, BLACK]
color_index = [0, 1, 2]

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_ESCAPE:
                running = False

            if event.key == pygame.K_SPACE:
                for obj in objects:
                    obj.is_animating = not obj.is_animating

            if event.key == pygame.K_1:
                select_object(0)
            if event.key == pygame.K_2:
                select_object(1)
            if event.key == pygame.K_3:
                select_object(2)

            if event.key == pygame.K_LEFT:
                objects[selected_index].x -= 10
            if event.key == pygame.K_RIGHT:
                objects[selected_index].x += 10
            if event.key == pygame.K_UP:
                objects[selected_index].y -= 10
            if event.key == pygame.K_DOWN:
                objects[selected_index].y += 10

            if event.key == pygame.K_PLUS or event.key == pygame.K_EQUALS:
                objects[selected_index].size += 5
            if event.key == pygame.K_MINUS:
                objects[selected_index].size -= 5
                if objects[selected_index].size < 10:
                    objects[selected_index].size = 10

            if event.key == pygame.K_r:
                objects[selected_index].angle += 15

            if event.key == pygame.K_c:
                color_index[selected_index] = (color_index[selected_index] + 1) % len(colors)
                objects[selected_index].color = colors[color_index[selected_index]]

        if event.type == pygame.MOUSEBUTTONDOWN:
            mx, my = event.pos
            for i, obj in enumerate(objects):
                if abs(mx - obj.x) < obj.size + 20 and abs(my - obj.y) < obj.size + 20:
                    select_object(i)
                    dragging = True
                    break

        if event.type == pygame.MOUSEBUTTONUP:
            dragging = False

        if event.type == pygame.MOUSEMOTION and dragging:
            objects[selected_index].x, objects[selected_index].y = event.pos

    screen.fill(WHITE)
    pygame.draw.line(screen, BLACK, (0, HEIGHT - 50), (WIDTH, HEIGHT - 50), 2)

    for obj in objects:
        obj.animate()
        obj.draw(screen)

    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
sys.exit()