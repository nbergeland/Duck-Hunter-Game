# Duck-Hunter-Game
Classic game of Duck Hunter coded in Python. Runs locally.

    import pygame
    import sys
    import random
    
    # Initialize Pygame
    pygame.init()
    
    # Screen dimensions
    WIDTH, HEIGHT = 800, 600
    SCREEN = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption('Duck Hunter (Shape-Based)')
    
    # Colors
    WHITE = (255, 255, 255)
    BLACK = (0, 0, 0)
    SKY_BLUE = (135, 206, 235)
    DUCK_COLOR = (255, 200, 0)
    CROSSHAIR_COLOR = (200, 0, 0)
    
    # Game settings
    FPS = 60
    DUCK_WIDTH, DUCK_HEIGHT = 60, 45
    SHOT_RADIUS = 20
    GAME_DURATION = 30  # seconds
    
    # Duck class (uses simple rectangle shape)
    class Duck:
        def __init__(self):
            self.x = -DUCK_WIDTH
            self.y = random.randint(50, HEIGHT - 100)
            self.speed = random.uniform(2, 5)
            self.rect = pygame.Rect(self.x, self.y, DUCK_WIDTH, DUCK_HEIGHT)

    def move(self):
        self.rect.x += self.speed

    def draw(self, screen):
        # Simple duck: rectangle body + circle head
        body = self.rect
        pygame.draw.ellipse(screen, DUCK_COLOR, body)
        head_center = (int(body.x + DUCK_WIDTH * 0.8), int(body.y + DUCK_HEIGHT * 0.3))
        head_radius = int(DUCK_HEIGHT * 0.3)
        pygame.draw.circle(screen, DUCK_COLOR, head_center, head_radius)
    
    # Draw crosshair
    def draw_crosshair(screen, pos):
        x, y = pos
        length = 20
        pygame.draw.line(screen, CROSSHAIR_COLOR, (x - length, y), (x + length, y), 2)
        pygame.draw.line(screen, CROSSHAIR_COLOR, (x, y - length), (x, y + length), 2)
        pygame.draw.circle(screen, CROSSHAIR_COLOR, pos, length, 1)
    
    # Draw current score and timer
    def draw_hud(screen, score, time_left, font):
        score_text = font.render(f"Score: {score}", True, BLACK)
        time_text = font.render(f"Time: {int(time_left)}", True, BLACK)
        screen.blit(score_text, (10, 10))
        screen.blit(time_text, (WIDTH - 150, 10))
    
    # Main game loop
    def main():
        clock = pygame.time.Clock()
        font = pygame.font.SysFont('Arial', 32)
        score = 0
        start_ticks = pygame.time.get_ticks()

    ducks = [Duck()]
    running = True

    while running:
        dt = clock.tick(FPS) / 1000
        elapsed = (pygame.time.get_ticks() - start_ticks) / 1000
        time_left = GAME_DURATION - elapsed
        if time_left <= 0:
            running = False

        # Event handling
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

        # Spawn more ducks occasionally
        if random.random() < 0.01:
            ducks.append(Duck())

        # Update ducks
        for duck in ducks[:]:
            duck.move()
            if duck.rect.x > WIDTH:
                ducks.remove(duck)

        # Drawing
        SCREEN.fill(SKY_BLUE)
        for duck in ducks:
            duck.draw(SCREEN)
        draw_crosshair(SCREEN, pygame.mouse.get_pos())
        draw_hud(SCREEN, score, time_left, font)

        pygame.display.flip()

    # Game over screen
    SCREEN.fill(BLACK)
    over_text = font.render(f"Game Over! Final Score: {score}", True, WHITE)
    SCREEN.blit(over_text, (WIDTH//2 - over_text.get_width()//2, HEIGHT//2 - 20))
    pygame.display.flip()
    pygame.time.delay(3000)
    pygame.quit()
    sys.exit()

    if __name__ == '__main__':
        main()

