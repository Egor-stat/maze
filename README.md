# maze
from pygame import *
mixer.init()
font.init()
window = display.set_mode((700, 500))
display.set_caption("Maze")
clock = time.Clock()
background = transform.scale(image.load("background.jpg"), (700, 500))
class GameSprite(sprite.Sprite):
    def __init__(self, picture_sprite, player_x, player_y, player_speed, scale):
        super().__init__()
        self.sprite = transform.scale(image.load(picture_sprite), scale)
        self.speed = player_speed
        self.rect = self.sprite.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y
    def reset(self):
        window.blit(self.sprite, (self.rect.x, self.rect.y))
class Player(GameSprite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_UP] and self.rect.y > 5:
            self.rect.y -= self.speed
        if keys[K_DOWN] and self.rect.y < 420:
            self.rect.y += self.speed
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < 620:
            self.rect.x += self.speed
class Enemy(GameSprite):
    direction = "left"
    def update(self):
        if self.rect.x <= 470:
            self.direction = "right"
        if self.rect.x >= 615:
            self.direction = "left"
        if self.direction == "left":
            self.rect.x -= self.speed
        else:
            self.rect.x += self.speed
class Wall(sprite.Sprite):
    def __init__(self, color_1, color_2, color_3, wall_x, wall_y, wall_width, wall_height):
        super().__init__()
        self.color_1 = color_1
        self.color_2 = color_2
        self.color_3 = color_3
        self.width = wall_width
        self.height = wall_height
        self.image = Surface((self.width, self.height))
        self.image.fill((self.color_1, self.color_2, self.color_3))
        self.rect = self.image.get_rect()
        self.rect.x = wall_x
        self.rect.y = wall_y
    def draw_wall(self):
        window.blit(self.image, (self.rect.x, self.rect.y))
class Bullet(GameSprite):
    def update(self):
        self.rect.x += self.speed

mixer.init()
mixer.music.load("jungles.ogg")
mixer.music.play()
game = True
finish = False
FPS = 60
player = Player("hero.png", 5, 420, 4, (65, 65))
enemy = Enemy("cyborg.png", 620, 310, 2, (65, 65))
outlaw_1 = Enemy("outlaw.png", 350, 140, 5, (65, 65))
outlaw_2 = Enemy("outlaw_mirror.png", 325, 425, 5, (65, 65))
final = GameSprite("treasure.png", 540, 420, 0, (65, 65))
wall1 = Wall(255, 255, 255, 15, 10, 670, 10)
wall2 = Wall(255, 255, 255, 115, 110, 480, 10)
wall3 = Wall(255, 255, 255, 115, 110, 10, 380)
wall4 = Wall(255, 255, 255, 215, 210, 480, 10)
wall5 = Wall(255, 255, 255, 215, 210, 10, 190)
wall6 = Wall(255, 255, 255, 215, 400, 210, 10)
wall7 = Wall(255, 255, 255, 425, 210, 10, 200)
wall8 = Wall(255, 255, 255, 525, 300, 10, 200)
wall9 = Wall(255, 255, 255, 525, 300, 80, 10)
wall10 = Wall(255, 255, 255, 605, 300, 10, 100)
walls = sprite.Group()
walls.add(wall1)
walls.add(wall2)
walls.add(wall3)
walls.add(wall4)
walls.add(wall5)
walls.add(wall6)
walls.add(wall7)
walls.add(wall8)
walls.add(wall9)
walls.add(wall10)
font = font.Font(None, 70)
win = font.render("YOU WIN!", True, (255, 215, 0))
lose = font.render("YOU LOSE!", True, (255, 215, 0))
bullets = sprite.Group()
while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
    if finish != True:
        keys = key.get_pressed()
        if keys[K_d]:
            bullet = Bullet("electric_ball.png", player.rect.x, player.rect.y, 2, (15, 15))
            bullets.add(bullet)
        bullets.update()
        bullets.draw(window)
        window.blit(background, (0, 0))
        player.update()
        enemy.update()
        outlaw_1.reset()
        outlaw_2.reset()
        player.reset()
        enemy.reset()
        final.reset()
        wall1.draw_wall()
        wall2.draw_wall()
        wall3.draw_wall()
        wall4.draw_wall()
        wall5.draw_wall()
        wall6.draw_wall()
        wall7.draw_wall()
        wall8.draw_wall()
        wall9.draw_wall()
        wall10.draw_wall()
        list_walls = sprite.spritecollide(
            player, walls, False, False
        )
        if sprite.collide_rect(player, enemy) or list_walls:
            finish = True
            window.blit(lose, (200, 200))
            mixer.music.load("kick.ogg")
            mixer.music.play()
        if sprite.collide_rect(player, final):
            finish = True
            window.blit(win, (200, 200))
            mixer.music.load("money.ogg")
            mixer.music.play()
    display.update()
    clock.tick(FPS)
   
