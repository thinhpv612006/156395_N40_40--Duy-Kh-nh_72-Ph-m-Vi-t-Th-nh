import pygame
import sys

# Khởi tạo Pygame
pygame.init()

# Cấu hình màn hình
WIDTH, HEIGHT = 800, 600  
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("3D Soccer Game Simulation")
clock = pygame.time.Clock()

# Màu sắc
GREEN = (34, 139, 34)
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Sân bóng
field = pygame.Rect(50, 50, WIDTH - 100, HEIGHT - 100)

# Tạo bóng
ball = pygame.Rect(WIDTH // 2 - 30, HEIGHT // 2, 20, 20)
ball_speed = [4, 4]

# Cầu thủ
players1 = [
    pygame.Rect(200, 150, 20, 20),
    pygame.Rect(200, 250, 20, 20),
    pygame.Rect(200, 350, 20, 20),
    pygame.Rect(200, 450, 20, 20),
    pygame.Rect(180, 300, 20, 20)
]
players2 = [
    pygame.Rect(WIDTH - 220, 150, 20, 20),
    pygame.Rect(WIDTH - 220, 250, 20, 20),
    pygame.Rect(WIDTH - 220, 350, 20, 20),
    pygame.Rect(WIDTH - 220, 450, 20, 20),
    pygame.Rect(WIDTH - 240, 300, 20, 20)
]
player_speed = 5

# Bàn thắng
goal_left = pygame.Rect(50, HEIGHT // 2 - 50, 10, 100)
goal_right = pygame.Rect(WIDTH - 60, HEIGHT // 2 - 50, 10, 100)

# Điểm số
score1, score2 = 0, 0
font = pygame.font.Font(None, 50)

def draw_field():
    pygame.draw.rect(screen, GREEN, field)
    pygame.draw.line(screen, WHITE, (WIDTH // 2, 50), (WIDTH // 2, HEIGHT - 50), 5)
    pygame.draw.ellipse(screen, WHITE, (WIDTH // 2 - 75, HEIGHT // 2 - 75, 150, 150), 5)
    pygame.draw.rect(screen, WHITE, goal_left, 5)
    pygame.draw.rect(screen, WHITE, goal_right, 5)

def handle_collision():
    global ball_speed

    # Va chạm với cầu thủ
    for player in players1 + players2:
        if ball.colliderect(player):
            ball_speed[0] = -ball_speed[0]
            ball_speed[1] = -abs(ball_speed[1])  # Đá bóng lên

    # Bảo đảm bóng không ra khỏi sân trên/dưới
    if ball.top <= 50 or ball.bottom >= HEIGHT - 50:
        ball_speed[1] = -ball_speed[1]

    # Bảo đảm bóng không ra khỏi biên sân bên trái hoặc bên phải
    if ball.left <= field.left or ball.right >= field.right:
        ball_speed[0] = -ball_speed[0]

def reset_ball():
    global ball, ball_speed
    ball.center = (WIDTH // 2, HEIGHT // 2)
    ball_speed[0] *= -1  # Đảo chiều tốc độ bóng

def show_winner(winner):
    screen.fill(BLACK)
    win_text = font.render(f"{winner} WINS!", True, WHITE)
    screen.blit(win_text, (WIDTH // 2 - win_text.get_width() // 2, HEIGHT // 2 - 50))
    pygame.display.flip()
    pygame.time.wait(3000)

def main():
    global score1, score2, ball, ball_speed

    running = True

    while running:
        screen.fill(BLACK)
        draw_field()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        keys = pygame.key.get_pressed()

        # Di chuyển cầu thủ người chơi 1 (W, A, S, D)
        if keys[pygame.K_w]:
            for player in players1:
                player.y -= player_speed
        if keys[pygame.K_s]:
            for player in players1:
                player.y += player_speed
        if keys[pygame.K_a]:
            for player in players1:
                player.x -= player_speed
        if keys[pygame.K_d]:
            for player in players1:
                player.x += player_speed

        # Di chuyển cầu thủ người chơi 2 (UP, DOWN, LEFT, RIGHT)
        if keys[pygame.K_UP]:
            for player in players2:
                player.y -= player_speed
        if keys[pygame.K_DOWN]:
            for player in players2:
                player.y += player_speed
        if keys[pygame.K_LEFT]:
            for player in players2:
                player.x -= player_speed
        if keys[pygame.K_RIGHT]:
            for player in players2:
                player.x += player_speed

        # Di chuyển bóng
        ball.x += ball_speed[0]
        ball.y += ball_speed[1]

        handle_collision()

        # Kiểm tra bàn thắng
        if ball.colliderect(goal_left):
            score2 += 1
            reset_ball()

        elif ball.colliderect(goal_right):
            score1 += 1
            reset_ball()

        if score1 == 5:
            show_winner("Player 1")
            running = False
        elif score2 == 5:
            show_winner("Player 2")
            running = False

        # Vẽ đối tượng
        pygame.draw.ellipse(screen, WHITE, ball)

        for player in players1:
            pygame.draw.rect(screen, RED, player)

        for player in players2:
            pygame.draw.rect(screen, BLUE, player)

        # Hiển thị điểm số
        score_text = font.render(f"{score1} - {score2}", True, WHITE)
        screen.blit(score_text, (WIDTH // 2 - score_text.get_width() // 2, 10))

        pygame.display.flip()
        clock.tick(60)

    pygame.quit()
    sys.exit()

if __name__ == "__main__":
    main()
