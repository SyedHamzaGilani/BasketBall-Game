import pygame
from pygame.locals import *
import random
import sys
pygame.init()
FPS = 32
FPSCLOCK = pygame.time.Clock()
SETWIDTH = 700
SETHEIGHT = 570
BACKGROUND = "Basketball Runner/background.png"
BASKET = "Basketball Runner/basket.png"
BASKETBALL = "Basketball Runner/basketball.png"
POLE = "Basketball Runner/pole.png"
ROAD = "Basketball Runner/road.jpg"
class Game:
    def __init__(self):
        self.screen = pygame.display.set_mode((SETWIDTH, SETHEIGHT), 0, 32)
        pygame.display.set_caption("Basket Ball Game By Syed Hamza Gilani")
        self.background = pygame.image.load(BACKGROUND).convert()
        self.road = Road(self.screen)
        self.basketball = BasketBall(self.screen)
        self.pole = Pole(self.screen)
        self.basket = Basket(self.screen)
        self.speed = 0
        self.score = 0
        self.basketScore = 0
        self.gethighest = 0
    def is_collide(self):
        if self.basketball.ballX >= self.pole.poleX and self.basketball.ballX <= self.pole.poleX + 50 and self.basketball.ballY >= self.pole.poleY:
            return True
        return False
    def increment(self):
        if (self.basket.basketX) < self.basketball.ballX and self.basket.basketY <= self.basketball.ballY:
            return True
        return False
    def getText(self,text,style,size,color,x,y,bold = False, itallic = False):
        font = pygame.font.SysFont(style,size,bold= bold, italic= itallic)
        write = font.render(text,True,color)
        self.screen.blit(write,(x,y))
    def getHighestScore(self):
        with open("HighestSxore.txt","r") as f:
            self.high = f.read()
            return self.high
    def play(self):
        run = True
        while run:
            self.screen.blit(self.background,(0,0))
            for events in pygame.event.get():
                if events.type == QUIT or (events.type == KEYDOWN and events.key == K_ESCAPE):
                    run = False
                    pygame.quit()
                    sys.exit()
                if events.type == KEYDOWN:
                    if events.key == K_SPACE and not self.is_collide():
                        self.basketball.upward += 5
                        self.speed = 5
                        self.gethighest = 0.001
                        self.road.road_vel = int(self.speed)
                        self.pole.pole_vel = int(self.speed)
                        self.basket.bas_vel = int(self.speed)
            self.pole.display()
            self.pole.move()
            self.basket.display()
            self.basket.move()
            self.road.display()
            self.road.move()
            self.basketball.display()
            self.basketball.bouncing()
            if self.is_collide():
                # gameover = pygame.mixer.Sound("GameOver.wav")
                # pygame.mixer.Sound.play(gameover)
                self.getText("Game Over","Algerian",60,(0, 0, 250),(SETWIDTH/2 - 130),SETHEIGHT/2, False, False)
                self.getText("Basket Ball Game By Syed Hamza Gilani", "Algerian", 25, (0, 250, 250), 35,(SETHEIGHT - 45), True, True)
                self.speed = 0
                self.road.roadX = 0
                self.pole. poleX = 0
                self.basket.basketX = 35
                self.basketball.upward = 0
                self.basketball.down = 0
                self.pole.pole_vel = 0
                self.road.road_vel = 0
            if self.increment():
                basket = pygame.mixer.Sound("basket.wav")
                pygame.mixer.Sound.play(basket)
                self.basketScore += 1
            self.speed += self.gethighest
            self.score += int(self.speed)
            self.getText(f"Score {self.score}","Algerian",20,(250, 0, 0),10, 10, False, False)
            self.getText(f"Basket Score {self.basketScore}","Algerian",20,(250, 0, 0),10, 30, False, False)
            try:
                self.highestScore = int(self.getHighestScore())
            except:
                self.highestScore = 0
            if self.highestScore < self.score:
                self.highestScore = self.score
                self.getText("Congratulations!, You Have Crossed The Highest Score.", "courier", 20, (250, 0, 0), 30,(SETHEIGHT- 50), True, True)
            with open("HighestSxore.txt","w") as f:
                f.write(str(self.highestScore))
            self.getText(f"Highest Score {self.getHighestScore()}","Algerian",20,(250, 0, 250),450,10,False, False)




            pygame.display.update()
            FPSCLOCK.tick(FPS)


class Road:
    def __init__(self,parentScreen):
        self.parentScreen = parentScreen
        self.road = pygame.image.load(ROAD).convert()
        self.roadX = 0
        self.roadY = SETHEIGHT - 70
        self.road_vel = 0
    def display(self):
        self.parentScreen.blit(self.road, (self.roadX, self.roadY))
        self.parentScreen.blit(self.road, (self.roadX + SETWIDTH, self.roadY))
    def move(self):
        self.roadX += -self.road_vel
        if self.roadX <= -SETWIDTH:
            self.roadX = 0

class Pole:
    def __init__(self,parentScreen):
        self.parentScreen = parentScreen
        self.pole = pygame.image.load(POLE).convert_alpha()
        self.poleX = SETWIDTH - 100
        self.road = Road(self.parentScreen)
        self.a = self.road.roadY
        self.poleY = self.getRandom()
        self.pole_vel = 0
    def getRandom(self):
        return random.randrange(100, self.a-200)
    def display(self):
        self.parentScreen.blit(self.pole, (self.poleX, self.poleY))
    def move(self):
        self.poleX -= self.pole_vel
        if self.poleX <= -100:
            self.poleX = SETWIDTH + 10
            self.poleY = self.getRandom()




class BasketBall:
    def __init__(self,parentScreen):
        self.parentScreen = parentScreen
        self.basketball = pygame.image.load(BASKETBALL).convert_alpha()
        self.ballX = 20
        self.ballY = SETHEIGHT - 70
        self.upward = 25
        self.down = 10
    def display(self):
        self.parentScreen.blit(self.basketball, (self.ballX, self.ballY))
    def bouncing(self):
        self.ballY -= self.upward
        self.upward -= 1
        self.ballY += self.down
        if self.ballY > SETHEIGHT - 70:
            bouncing = pygame.mixer.Sound("bouncing.wav")
            pygame.mixer.Sound.play(bouncing)
            self.upward = 25
            self.down = 10

class Basket:
    def __init__(self,parentScreen):
        self.parentScreen = parentScreen
        self.basket = pygame.image.load(BASKET).convert_alpha()
        self.pole = Pole(self.parentScreen)
        self.road = Road(self.parentScreen)
        self.a = self.pole.poleY + 100
        self.b = self.road.roadY - 100
        self.basketX = self.pole.poleX + 35
        self.basketY = self.getRandom()
        self.bas_vel = 0
    def getRandom(self):
        return random.randrange(self.a, self.b)
    def display(self):
        self.parentScreen.blit(self.basket, (self.basketX, self.basketY))
    def move(self):
        self.basketX -= self.bas_vel
        if self.basketX <= -100:
            self.basketX = SETWIDTH + 10
            self.basketY = self.getRandom()



if __name__ == '__main__':
    pygame.mixer.music.load("starting.mp3")
    pygame.mixer.music.play(100)
    game = Game()
    game.play()