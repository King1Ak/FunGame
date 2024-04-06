import pygame
import time
import random
pygame.font.init()

WIDTH, HEIGHT = 900, 700
Win =  pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space Dodge")

Player_Width = 40
Player_Height = 60
Player_Velocity = 10
Font = pygame.font.SysFont("comicans", 30)
Star_Width = 10
Star_Height = 20
Star_Velocity = 3

BG = pygame.transform.scale(pygame.image.load("jpeg2.bg"), (WIDTH, HEIGHT))
pygame.display.update()
def draw(player, elasped_time, stars, points):
    Win.blit(BG, (0, 0))

    time_text = Font.render(f"Time: {round(elasped_time)}", 1, "white")
    Win.blit(time_text, (10, 10))
    Points_text = Font.render(f"Points: {round(points)}", 1, "white")
    Win.blit(Points_text, (10,40))

    pygame.draw.rect(Win, "red", player)

    for star in stars:
        pygame.draw.rect(Win, "white", star)

    pygame.display.update()
def main():
    run = True

    player = pygame.Rect(200, HEIGHT - Player_Height,
                         Player_Width, Player_Height)
    clock = pygame.time.Clock()
    start_time = time.time()
    elasped_time = 0

    star_add_increment = 2000
    star_count = 0


    stars = []
    hit = False
    points = 0
    win = False

    while run:
        star_count += clock.tick(60)
        elasped_time = time.time() - start_time

        if star_count > star_add_increment :
            for _ in range(3):
                star_x = random.randint(0, WIDTH - Star_Width)
                star = pygame.Rect(star_x, - Star_Height, Star_Width, Star_Height)
                stars.append(star)
            star_add_increment = max(200, star_add_increment - 50)
            star_count = 0
            if elasped_time == 30:
                break
            if star_count > star_add_increment:
                for _ in range(6):
                    star_x = random.randint(0, WIDTH - Star_Width)
                    star = pygame.Rect(star_x, - Star_Height, Star_Width, Star_Height)
                    stars.append(star)
                star_add_increment = max(200, star_add_increment - 50)
                star_count = 0
            if elasped_time == 60:
                win = True


        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                run = False
                break

        keys = pygame.key.get_pressed()
        if keys[pygame.K_a] and player.x - Player_Velocity >= 0:
            player.x -= Player_Velocity
        if keys[pygame.K_d] and player.x + Player_Velocity + Player_Width <= WIDTH:
            player.x += Player_Velocity
        if keys[pygame.K_w] and player.y - Player_Velocity >= 0:
            player.y -= Player_Velocity
        if keys[pygame.K_s] and player.y + Player_Velocity + Player_Width <= HEIGHT:
            player.y += Player_Velocity

        for star in stars[:]:
            star.y += Star_Velocity
            if star.y > HEIGHT:
                stars.remove(star)
                points += 1
            elif star.y + star.height >= player.y and star.colliderect(player):
                stars.remove(star)
                hit = True
                break


        if hit:
            lost_text = Font.render("You Lost!", 1, "white")
            Win.blit(lost_text, (WIDTH/2 - lost_text.get_width()/2, HEIGHT/2 - lost_text.get_height()/2))
            pygame.display.update()
            pygame.time.delay(4000)
            break
        if win:
            win_text = Font.render("You Win1", 1, "white")
            Win.blit(win_text, (WIDTH/2 - win_text.get_width()/2, HEIGHT/2 - win_text.get_height()/2))
            pygame.display.update()
            pygame.time.delay(4000)
            break


        draw(player, elasped_time, stars, points)
    pygame.quit()

if __name__ == "__main__":
    main()


