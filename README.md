import pygame
# Initialize Pygame
pygame.init()
# Screen dimensions
screen_width = 800
screen_height = 600
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Platformer")
# Colors
black = (0, 0, 0)
white = (255, 255, 255)
# Player class
class Player(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((40, 60))
        self.image.fill(white)
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y
        self.x_vel = 0
        self.y_vel = 0
        self.is_jumping = False
    def update(self):
        # Apply horizontal movement
        self.rect.x += self.x_vel
        # Apply gravity
        self.y_vel += 0.5
        self.rect.y += self.y_vel
        # Check for collisions with the ground
        if self.rect.bottom >= screen_height:
            self.rect.bottom = screen_height
            self.y_vel = 0
            self.is_jumping = False
    def jump(self):
        if not self.is_jumping:
            self.y_vel = -10
            self.is_jumping = True
# Platform class
class Platform(pygame.sprite.Sprite):
    def __init__(self, x, y, width, height):
        super().__init__()
        self.image = pygame.Surface((width, height))
        self.image.fill(white)
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y
# Sprite groups
all_sprites = pygame.sprite.Group()
platforms = pygame.sprite.Group()
# Create player
player = Player(50, screen_height - 100)
all_sprites.add(player)
# Create platforms
platform1 = Platform(0, screen_height - 50, 200, 20)
platforms.add(platform1)
platform2 = Platform(300, screen_height - 150, 100, 20)
platforms.add(platform2)
platform3 = Platform(500, screen_height - 250, 150, 20)
platforms.add(platform3)
all_sprites.add(platforms)
# Game loop
running = True
while running:
    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                player.x_vel = -5
            if event.key == pygame.K_RIGHT:
                player.x_vel = 5
            if event.key == pygame.K_SPACE:
                player.jump()
        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                player.x_vel = 0
    # Update sprites
    all_sprites.update()
    # Check for collisions
    hits = pygame.sprite.spritecollide(player, platforms, False)
    if hits:
        player.rect.bottom = hits[0].rect.top
        player.y_vel = 0
        player.is_jumping = False
    # Draw everything
    screen.fill(black)
    all_sprites.draw(screen)
    # Update the display
    pygame.display.flip()
# Quit Pygame
pygame.quit()
