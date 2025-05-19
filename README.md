import pygame
import random
import sys

# Initialize Pygame
pygame.init()

# Screen settings
WIDTH, HEIGHT = 600, 800
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space Runner")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# Load assets
player_img = pygame.Surface((50, 50))
player_img.fill((0, 255, 0))

enemy_img = pygame.Surface((50, 50))
enemy_img.fill((255, 0, 0))

bullet_img = pygame.Surface((5, 15))
bullet_img.fill((255, 255, 0))

# Clock
clock = pygame.time.Clock()
FPS = 60

# Game objects
player_rect = player_img.get_rect(center=(WIDTH // 2, HEIGHT - 60))
player_speed = 5

bullets = []
bullet_speed = 7

enemies = []
enemy_speed = 3
enemy_spawn_delay = 30
enemy_timer = 0

score = 0
font = pygame.font.SysFont(None, 36)

# Bullet firing cooldown variables
bullet_cooldown = 0
bullet_cooldown_delay = 10  # frames to wait between bullets

# Game loop
running = True
while running:
    screen.fill(BLACK)

    # Event Handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Player movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_rect.left > 0:
        player_rect.x -= player_speed
    if keys[pygame.K_RIGHT] and player_rect.right < WIDTH:
        player_rect.x += player_speed

    # Continuous shooting with SPACE key (using cooldown)
    if keys[pygame.K_SPACE]:
        if bullet_cooldown == 0 and len(bullets) < 10:
            bullet_rect = bullet_img.get_rect(midbottom=player_rect.midtop)
            bullets.append(bullet_rect)
            bullet_cooldown = bullet_cooldown_delay

    if bullet_cooldown > 0:
        bullet_cooldown -= 1

    # Update bullets
    for bullet in bullets[:]:
        bullet.y -= bullet_speed
        if bullet.bottom < 0:
            bullets.remove(bullet)

    # Spawn enemies
    enemy_timer += 1
    if enemy_timer >= enemy_spawn_delay:
        enemy_timer = 0
        x_pos = random.randint(0, WIDTH - 50)
        enemy_rect = enemy_img.get_rect(topleft=(x_pos, 0))
        enemies.append(enemy_rect)

    # Update enemies
    for enemy in enemies[:]:
        enemy.y += enemy_speed
        if enemy.top > HEIGHT:
            enemies.remove(enemy)

    # Collision detection
    for bullet in bullets[:]:
        for enemy in enemies[:]:
            if bullet.colliderect(enemy):
                bullets.remove(bullet)
                enemies.remove(enemy)
                score += 10
                break

    # Draw player
    screen.blit(player_img, player_rect)

    # Draw bullets
    for bullet in bullets:
        screen.blit(bullet_img, bullet)

    # Draw enemies
    for enemy in enemies:
        screen.blit(enemy_img, enemy)

    # Draw score
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

    # Update display
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
sys.exit()
