# import pygame
import random
import math

# Khởi tạo pygame
pygame.init()

# Tạo màn hình
screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("🚀 Game Bắn Súng")

# Màu sắc
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)

# Người chơi
player_img = pygame.image.load('player.png')  # Tải ảnh phi thuyền
player_x = 370
player_y = 480
player_x_change = 0

# Đạn
bullet_img = pygame.image.load('bullet.png')
bullet_x = 0
bullet_y = 480
bullet_y_change = 10
bullet_state = "ready"  # 'ready' = sẵn sàng, 'fire' = đang bay

# Kẻ thù
enemy_img = pygame.image.load('enemy.png')
enemy_x = random.randint(0, 736)
enemy_y = random.randint(50, 150)
enemy_x_change = 3
enemy_y_change = 40

# Hàm vẽ người chơi
def player(x, y):
    screen.blit(player_img, (x, y))

# Hàm bắn đạn
def fire_bullet(x, y):
    global bullet_state
    bullet_state = "fire"
    screen.blit(bullet_img, (x + 16, y + 10))

# Hàm kiểm tra va chạm
def is_collision(enemy_x, enemy_y, bullet_x, bullet_y):
    distance = math.sqrt((enemy_x - bullet_x)**2 + (enemy_y - bullet_y)**2)
    return distance < 27

# Vòng lặp game chính
running = True
while running:
    screen.fill(BLACK)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # Điều khiển
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                player_x_change = -5
            if event.key == pygame.K_RIGHT:
                player_x_change = 5
            if event.key == pygame.K_SPACE:
                if bullet_state == "ready":
                    bullet_x = player_x
                    fire_bullet(bullet_x, bullet_y)

        if event.type == pygame.KEYUP:
            if event.key in [pygame.K_LEFT, pygame.K_RIGHT]:
                player_x_change = 0

    # Cập nhật vị trí người chơi
    player_x += player_x_change
    player_x = max(0, min(736, player_x))

    # Cập nhật vị trí kẻ thù
    enemy_x += enemy_x_change
    if enemy_x <= 0 or enemy_x >= 736:
        enemy_x_change *= -1
        enemy_y += enemy_y_change

    # Cập nhật đạn
    if bullet_y <= 0:
        bullet_y = 480
        bullet_state = "ready"
    if bullet_state == "fire":
        fire_bullet(bullet_x, bullet_y)
        bullet_y -= bullet_y_change

    # Kiểm tra va chạm
    if is_collision(enemy_x, enemy_y, bullet_x, bullet_y):
        bullet_y = 480
        bullet_state = "ready"
        enemy_x = random.randint(0, 736)
        enemy_y = random.randint(50, 150)

    player(player_x, player_y)
    screen.blit(enemy_img, (enemy_x, enemy_y))

    pygame.display.update()


