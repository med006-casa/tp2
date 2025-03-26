#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Date {
    char jour[3];
    char mois[3];
    char annee[5];
} date;

typedef struct Produit {
    char *Nom_produit;
    char Reference[100];
    float Montant;
    date DateAchat;
} produit;

typedef struct Liste {
    produit cellule;
    struct Liste *psuiv;
} liste;

liste* creer_liste_produits() {
    return NULL;
}

int est_vide(liste *tete) {
    return tete == NULL;
}

liste* creer_produit() {
    liste* nouveau = (liste*)malloc(sizeof(liste));
    if (!nouveau) 
        exit(1);
    }

    nouveau->cellule.Nom_produit = (char*)malloc(50 * sizeof(char));
    if (!nouveau->cellule.Nom_produit) {
        free(nouveau);
        exit(1);
    }

    printf("Nom du produit: ");
    scanf("%s", nouveau->cellule.Nom_produit);
    printf("Référence du produit: ");
    scanf("%s", nouveau->cellule.Reference);
    printf("Montant: ");
    scanf("%f", &nouveau->cellule.Montant);
    printf("Date d'achat (jj mm aaaa): ");
    scanf("%s %s %s", nouveau->cellule.DateAchat.jour, 
          nouveau->cellule.DateAchat.mois, 
          nouveau->cellule.DateAchat.annee);

    nouveau->psuiv = NULL;
    return nouveau;
}

void afficher_produit(liste *produit) {
    if (produit) {
        printf("Nom: %s | Référence: %s | Montant: %.2f | Date: %s/%s/%s\n",
               produit->cellule.Nom_produit, produit->cellule.Reference, 
               produit->cellule.Montant, produit->cellule.DateAchat.jour, 
               produit->cellule.DateAchat.mois, produit->cellule.DateAchat.annee);
    }
}

liste* ajouter_debut(liste *tete) {
    liste *nouveau = creer_produit();
    nouveau->psuiv = tete;
    return nouveau;
}

void ajouter_fin(liste **tete) {
    liste *nouveau = creer_produit();
    if (*tete == NULL) {
        *tete = nouveau;
    } else {
        liste *temp = *tete;
        while (temp->psuiv != NULL) {
            temp = temp->psuiv;
        }
        temp->psuiv = nouveau;
    }
}

void afficher_produits(liste *tete) {
    if (est_vide(tete)) {
        printf("La liste est vide.\n");
        return;
    }
    liste *temp = tete;
    while (temp != NULL) {
        afficher_produit(temp);
        temp = temp->psuiv;
    }
}

int longueur(liste *tete) {
    int count = 0;
    liste *temp = tete;
    while (temp != NULL) {
        count++;
        temp = temp->psuiv;
    }
    return count;
}

void filtrer_date(liste *tete, date d) {
    liste *temp = tete;
    while (temp != NULL) {
        if (strcmp(temp->cellule.DateAchat.jour, d.jour) == 0 &&
            strcmp(temp->cellule.DateAchat.mois, d.mois) == 0 &&
            strcmp(temp->cellule.DateAchat.annee, d.annee) == 0) {
            afficher_produit(temp);
        }
        temp = temp->psuiv;
    }
}

liste* supprimer_debut(liste *tete) {
    if (est_vide(tete)) {
        printf("La liste est déjà vide.\n");
        return NULL;
    }
    liste *temp = tete;
    tete = tete->psuiv;
    free(temp->cellule.Nom_produit);
    free(temp);
    return tete;
}
void supprimer_fin(liste **tete) {
    if (*tete == NULL) {
        printf("La liste est déjà vide.\n");
        return;
    }
    if ((*tete)->psuiv == NULL) {
        free((*tete)->cellule.Nom_produit);
        free(*tete);
        *tete = NULL;
        return;
    }
    liste *temp = *tete;
    while (temp->psuiv->psuiv != NULL) {
        temp = temp->psuiv;
    }
    free(temp->psuiv->cellule.Nom_produit);
    free(temp->psuiv);
    temp->psuiv = NULL;
}

int main() {
    liste *maListe = creer_liste_produits();
    int choix;
    date d;

    do {
        printf("\nMenu:\n");
        printf("1. Ajouter un produit au début\n");
        printf("2. Ajouter un produit à la fin\n");
        printf("3. Afficher les produits\n");
        printf("4. Supprimer le premier produit\n");
        printf("5. Supprimer le dernier produit\n");
        printf("6. Afficher la longueur de la liste\n");
        printf("7. Filtrer les produits par date\n");
        printf("0. Quitter\n");
        printf("Votre choix: ");
        scanf("%d", &choix);

        switch (choix) {
            case 1:
                maListe = ajouter_debut(maListe);
                break;
            case 2:
                ajouter_fin(&maListe);
                break;
            case 3:
                afficher_produits(maListe);
                break;
            case 4:
                maListe = supprimer_debut(maListe);
                break;
            case 5:
                supprimer_fin(&maListe);
                break;
            case 6:
                printf("Nombre de produits: %d\n", longueur(maListe));
                break;
            case 7:
                printf("Entrez la date à filtrer (jj mm aaaa): ");
                scanf("%s %s %s", d.jour, d.mois, d.annee);
                filtrer_date(maListe, d);
                break;
            case 0:
                printf("Programme terminé.\n");
                break;
            default:
                printf("Choix invalide, veuillez réessayer.\n");
        }
    } while (choix != 0);

    return 0;
}
