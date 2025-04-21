# Duck-Hunter-Game
Classic game of Duck Hunter coded in Python. Runs locally.

    # This code is a simple duck hunting game where the player clicks on ducks to score points.
    # The game features a mallard duck design, a crosshair for aiming, and a HUD displaying the score and time left.
    # The game ends after a set duration, and the final score is displayed.
    # The game is implemented using Pygame, a popular library for creating games in Python.
    # The code includes classes and functions for managing the game state, drawing graphics, and handling user input.
        
    import pygame
    import sys
    import random
    
    # Initialize Pygame
    pygame.init()
    
    # Screen dimensions
    WIDTH, HEIGHT = 800, 600
    SCREEN = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption('Duck Hunter (Mallard Edition)')
    
    # Colors
    WHITE = (255, 255, 255)
    BLACK = (0, 0, 0)
    SKY_BLUE = (135, 206, 235)
    MALLARD_BODY = (139, 69, 19)       # Brown chest
    MALLARD_WING = (200, 200, 200)     # Grey body
    MALLARD_HEAD = (0, 128, 0)         # Green head
    MALLARD_BILL = (255, 215, 0)       # Yellow bill
    CROSSHAIR_COLOR = (200, 0, 0)
    
    # Game settings
    FPS = 60
    DUCK_WIDTH, DUCK_HEIGHT = 60, 45
    GAME_DURATION = 30  # seconds
    
    class Duck:
        def __init__(self):
            x = -DUCK_WIDTH
            y = random.randint(50, HEIGHT - 100)
            speed = random.uniform(2, 5)
            self.rect = pygame.Rect(x, y, DUCK_WIDTH, DUCK_HEIGHT)
            self.speed = speed

    def move(self):
        self.rect.x += self.speed

    def draw(self, screen):
        body = self.rect
        pygame.draw.ellipse(screen, MALLARD_WING, body)  # body
        chest = pygame.Rect(
            body.x + int(body.width * 0.1),
            body.y + int(body.height * 0.4),
            int(body.width * 0.5), int(body.height * 0.4)
        )
        pygame.draw.ellipse(screen, MALLARD_BODY, chest)  # chest
        head_center = (
            body.x + int(body.width * 0.8),
            body.y + int(body.height * 0.3)
        )
        pygame.draw.circle(screen, MALLARD_HEAD, head_center, int(body.height * 0.35))  # head
        pygame.draw.circle(
            screen, WHITE,
            (head_center[0] - int(body.height * 0.05), head_center[1]),
            int(body.height * 0.2), 3
        )  # neck ring
        bill = [
            (head_center[0] + int(body.height * 0.35), head_center[1]),
            (head_center[0] + int(body.height * 0.5), head_center[1] - int(body.height * 0.1)),
            (head_center[0] + int(body.height * 0.5), head_center[1] + int(body.height * 0.1))
        ]
        pygame.draw.polygon(screen, MALLARD_BILL, bill)  # bill


    def draw_crosshair(screen, pos):
        x, y = pos
        length = 20
        pygame.draw.line(screen, CROSSHAIR_COLOR, (x - length, y), (x + length, y), 2)
        pygame.draw.line(screen, CROSSHAIR_COLOR, (x, y - length), (x, y + length), 2)
        pygame.draw.circle(screen, CROSSHAIR_COLOR, pos, length, 1)
    
    
    def draw_hud(screen, score, time_left, font):
        score_text = font.render(f"Score: {score}", True, BLACK)
        time_text = font.render(f"Time: {int(time_left)}", True, BLACK)
        screen.blit(score_text, (10, 10))
        screen.blit(time_text, (WIDTH - 150, 10))
    
    
    def main():
        clock = pygame.time.Clock()
        font = pygame.font.SysFont('Arial', 32)
        score = 0
        start_ticks = pygame.time.get_ticks()
        ducks = [Duck()]
        running = True

    while running:
        dt = clock.tick(FPS)
        elapsed = (pygame.time.get_ticks() - start_ticks) / 1000
        time_left = GAME_DURATION - elapsed
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
                mx, my = pygame.mouse.get_pos()
                for duck in ducks[:]:
                    if duck.rect.collidepoint(mx, my):
                        score += 1
                        ducks.remove(duck)
                        break

        if time_left <= 0:
            running = False

        if random.random() < 0.01:
            ducks.append(Duck())

        for duck in ducks[:]:
            duck.move()
            if duck.rect.x > WIDTH:
                ducks.remove(duck)

        SCREEN.fill(SKY_BLUE)
        for duck in ducks:
            duck.draw(SCREEN)
        draw_crosshair(SCREEN, pygame.mouse.get_pos())
        draw_hud(SCREEN, score, time_left, font)
        pygame.display.flip()

    # Game Over screen loop
    SCREEN.fill(BLACK)
    over_text = font.render(f"Game Over! Final Score: {score}", True, WHITE)
    SCREEN.blit(over_text, (WIDTH // 2 - over_text.get_width() // 2, HEIGHT // 2 - 20))
    pygame.display.flip()

    gameover = True
    while gameover:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                gameover = False
        pygame.time.delay(100)

    pygame.quit()
    sys.exit()


    if __name__ == '__main__':
        main()

![Screenshot: Duck Hunter](duck.png)
