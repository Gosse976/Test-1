# Test-1
import pygame
import random
# Initialisation de Pygame
pygame.init()

# Paramètres de l'écran
largeur = 800
hauteur = 600
ecran = pygame.display.set_mode((largeur, hauteur))
pygame.display.set_caption("Vaisseaux de Guerre Rétro")

# Couleurs
blanc = (255, 255, 255)
noir = (0, 0, 0)
rouge = (255, 0, 0)

# Vaisseau joueur
vaisseau_joueur_img = pygame.Surface((50, 30))
vaisseau_joueur_img.fill(blanc)
vaisseau_joueur_rect = vaisseau_joueur_img.get_rect(center=(largeur // 2, hauteur - 50))
vitesse_vaisseau_joueur = 5

# Missile du joueur
missile_joueur_img = pygame.Surface((5, 15))
missile_joueur_img.fill(rouge)
missiles_joueur = []
vitesse_missile_joueur = 10

# Ennemi
ennemi_img = pygame.Surface((30, 20))
ennemi_img.fill(blanc)
ennemis = []
vitesse_ennemi = 2
vitesse_missile_ennemi = 5

# Score
score = 0
font = pygame.font.Font(None, 36)

# Boucle principale du jeu
running = True
clock = pygame.time.Clock()

def creer_ennemi():
    x = random.randint(0, largeur - 30)
    y = random.randint(-100, -20)
    ennemis.append(pygame.Rect(x, y, 30, 20))

for _ in range(10):  # Création d'une vague initiale d'ennemis
    creer_ennemi()

while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                missiles_joueur.append(missile_joueur_img.get_rect(center=vaisseau_joueur_rect.midtop))

    # Déplacement du vaisseau joueur
    touches = pygame.key.get_pressed()
    if touches[pygame.K_LEFT] and vaisseau_joueur_rect.left > 0:
        vaisseau_joueur_rect.x -= vitesse_vaisseau_joueur
    if touches[pygame.K_RIGHT] and vaisseau_joueur_rect.right < largeur:
        vaisseau_joueur_rect.x += vitesse_vaisseau_joueur

    # Déplacement des missiles du joueur
    for missile in missiles_joueur[:]:
        missile.y -= vitesse_missile_joueur
        if missile.bottom < 0:
            missiles_joueur.remove(missile)

    # Déplacement des ennemis
    for ennemi in ennemis:
        ennemi.y += vitesse_ennemi
        if ennemi.top > hauteur:
            ennemis.remove(ennemi)
            creer_ennemi()  # Remplacer l'ennemi sorti de l'écran

    # Collisions
    for missile in missiles_joueur[:]:
        for ennemi in ennemis[:]:
            if missile.colliderect(ennemi):
                missiles_joueur.remove(missile)
                ennemis.remove(ennemi)
                creer_ennemi()
                score += 10

    # Dessin
    ecran.fill(noir)
    ecran.blit(vaisseau_joueur_img, vaisseau_joueur_rect)
    for missile in missiles_joueur:
        ecran.blit(missile_joueur_img, missile)
    for ennemi in ennemis:
        ecran.blit(ennemi_img, ennemi)
    score_texte = font.render(f"Score: {score}", True, blanc)
    ecran.blit(score_texte, (10, 10))
    pygame.display.flip()

    clock.tick(60)

pygame.quit()
