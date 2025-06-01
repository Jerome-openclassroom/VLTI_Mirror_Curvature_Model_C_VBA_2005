#include "cpgplot.h" /* Librairie pour le traçage des graphiques en langage C */
#include <math.h>    /* Librairie des fonctions mathématiques */
#include <stdio.h>   /* Librairie des entrées/sorties standard */

int main() {
    int i, c, choix, num; /* Variables pour le choix du miroir et contrôle */
    float xr[201], yr[201]; /* Tableau pour la courbe de montée */
    float xs[201], ys[201]; /* Tableau pour la courbe de descente */
    float nsup = 0.0; /* Variable pour l'ajustement de l'échelle du graphique */
    double Ps; /* Pression séquentielle (Pseq) */
    double Pw, A1, A3, A5, C0, Alpha, Beta, Gamma; /* Constantes pour chaque VCM */
    double Cf1, Pf1, Cf2, Pf2, Cf3, Pf3, Cf4, Pf4, Cf5, Pf5, Cf6, Pf6; /* Mesures Fizeau */
    double Delta1, Delta3, Delta5; /* Variables pour l'équation de l'hystérèse */
    double A, B, C, coeff; /* Variables de contrôle des calculs */
    double X0, Y0, D; /* Variables pour l'équation de l'hystérèse */
    char buffer[20]; /* Buffer pour le nom du fichier Fizeau */
    FILE *fptr1, *fptr2; /* Pointeurs pour les fichiers Shack-Hartmann et Fizeau */

    /* Demande du numéro du miroir */
    printf("Entrez le numéro du miroir (entre 18 et 28) : \n");
    scanf("%d", &choix);
    sprintf(buffer, "fizeau/fizeau-m%d.dat", choix); /* Construction du chemin du fichier */

    /* Ouverture des fichiers */
    fptr1 = fopen("fi_fi.dat", "r"); /* Fichier Shack-Hartmann */
    if (fptr1 == NULL) {
        printf("Erreur : impossible d'ouvrir fi_fi.dat\n");
        return 1;
    }
    fptr2 = fopen(buffer, "r"); /* Fichier Fizeau */
    if (fptr2 == NULL) {
        printf("Erreur : impossible d'ouvrir %s\n", buffer);
        return 1;
    }

    /* Demande de la valeur de Pseq */
    printf("Diminution de la pression miroir # %d\n", choix);
    printf("Entrez la valeur de Pseq pour le second graphique (Pseq < 8 bars) : \n");
    scanf("%lf", &Ps);

    /* Lecture des fichiers et traitement */
    c = 1;
    do {
        /* Lecture des données Shack-Hartmann */
        fscanf(fptr1, "%d %lf %lf %lf %lf %lf %lf %lf %lf", &num, &Pw, &A1, &A3, &A5, &C0, &Alpha, &Beta, &Gamma);
        printf("%d %lf %lf %lf %lf %lf %lf %lf %lf\n", num, Pw, A1, A3, A5, C0, Alpha, Beta, Gamma);

        /* Lecture des données Fizeau */
        fscanf(fptr2, "%lf %lf %lf %lf %lf %lf %lf %lf %lf %lf %lf %lf", 
               &Cf1, &Pf1, &Cf2, &Pf2, &Cf3, &Pf3, &Cf4, &Pf4, &Cf5, &Pf5, &Cf6, &Pf6);
        printf("%lf %lf %lf %lf %lf %lf %lf %lf %lf %lf %lf %lf\n", 
               Cf1, Pf1, Cf2, Pf2, Cf3, Pf3, Cf4, Pf4, Cf5, Pf5, Cf6, Pf6);

        /* Calcul de X0 et Y0 */
        X0 = Alpha * Ps;
        Y0 = Beta * pow(Ps, 2) + Gamma * pow(Ps, 4);
        printf("Valeur de X0 : %lf, valeur de Y0 : %lf\n", X0, Y0);

        /* Calcul des Delta */
        D = pow(pow(X0, 2) - pow(Ps, 2), 2);
        Delta1 = Y0 * (-5 * (pow(X0, 2) * pow(Ps, 2)) + 3 * pow(Ps, 4)) / (2 * X0 * D);
        Delta3 = Y0 * (5 * pow(X0, 4) - pow(Ps, 4)) / (2 * pow(X0, 3) * D);
        Delta5 = Y0 * (-3 * pow(X0, 2) + pow(Ps, 2)) * (2 * pow(X0, 3) * D);
        printf("Valeur de Delta1 : %lf Delta3 : %lf Delta5 : %lf\n", Delta1, Delta3, Delta5);
        printf("Valeur de Pseq : %lf\n", Ps);
        printf("Valeur de D : %lf\n", D);

        /* Tracé du graphique 1 (courbes de montée et descente) */
        if (num == choix) {
            for (i = 0; i < 201; i++) {
                xr[i] = (12.0 / 200) * i;
                yr[i] = A1 * (xr[i] - C0) + A3 * pow((xr[i] - C0), 3) + A5 * pow((xr[i] - C0), 5); /* Montée */
                xs[i] = (Ps / 200) * i;
                coeff = Delta1 * xs[i] + Delta3 * pow(xs[i], 3) + Delta5 * pow(xs[i], 5); /* Hystérèse */
                A = yr[i];
                B = coeff;
                C = A - B;
                ys[i] = C; /* Descente */
                printf("A=%lf B=%lf C=%lf xr[%d]=%lf yr[%d]=%lf coeff=%lf ys[%d]=%lf\n", 
                       A, B, C, i, xr[i], i, yr[i], coeff, i, ys[i]);
            }

            if (cpgopen("/XWINDOW") < 1) {
                printf("Erreur : impossible d'ouvrir la fenêtre graphique\n");
                return 1;
            }
            cpgenv(0.0, 12.0, 0.0, 8.0, 0, 0); /* Échelle du graphique */
            cpgpt1(6.0, 0.5, 8); /* Légende */
            cpgtext(6.2, 0.5, "Mesures au Fizeau");
            cpgtext(6.0, 1.5, "Montée en pression");
            cpgsci(2); /* Couleur rouge */
            cpgtext(6.0, 1.0, "Réduction de la pression");
            cpgsci(1); /* Retour à la couleur blanche */
            cpglab("Courbure C-C0 (m^-1)", "Pression (bar)", "Calibration du miroir");

            /* Affichage des points Fizeau */
            printf("Fizeau courbure : %lf pression : %lf\n", Cf1, Pf1);
            printf("Fizeau courbure : %lf pression : %lf\n", Cf2, Pf2);
            printf("Fizeau courbure : %lf pression : %lf\n", Cf3, Pf3);
            printf("Fizeau courbure : %lf pression : %lf\n", Cf4, Pf4);
            printf("Fizeau courbure : %lf pression : %lf\n", Cf5, Pf5);
            printf("Fizeau courbure : %lf pression : %lf\n", Cf6, Pf6);

            cpgslw(3); /* Largeur de la courbe */
            cpgline(200, xr, yr); /* Courbe de montée */
            cpgpt1(Cf1, Pf1, 8); /* Points Fizeau */
            cpgpt1(Cf2, Pf2, 8);
            cpgpt1(Cf3, Pf3, 8);
            cpgpt1(Cf4, Pf4, 8);
            cpgpt1(Cf5, Pf5, 8);
            cpgpt1(Cf6, Pf6, 8);

            cpgslw(3);
            cpgsls(2); /* Courbe en pointillés */
            cpgsci(2); /* Couleur rouge */
            cpgline(200, xr, ys); /* Courbe de descente */

            getchar(); /* Pause pour afficher le graphique */
            cpgclos();
            c = 0;
        }

        /* Tracé du graphique 2 (hystérèse) */
        for (i = 0; i < 201; i++) {
            xr[i] = (Ps / 200) * i;
            yr[i] = Delta1 * xr[i] + Delta3 * pow(xr[i], 3) + Delta5 * pow(xr[i], 5);
            printf("xr[%d]=%lf yr[%d]=%lf\n", i, xr[i], i, yr[i]);
            if (yr[i] > nsup) nsup = yr[i]; /* Ajustement de l'échelle */
        }

        if (cpgopen("/XWINDOW") < 1) {
            printf("Erreur : impossible d'ouvrir la fenêtre graphique\n");
            return 1;
        }

        /* Ajustement de l'échelle du graphique 2 */
        if (Ps > 3.5 && Ps <= 4.2) cpgenv(0.0, Ps, 0.0, 0.04, 0, 0);
        else if (Ps > 3.0 && Ps <= 3.5) cpgenv(0.0, Ps, 0.0, 0.03, 0, 0);
        else if (Ps <= 3.0) cpgenv(0.0, Ps, 0.0, 0.02, 0, 0);
        else cpgenv(0.0, Ps, 0.0, nsup + 0.015, 0, 0);

        cpglab("Pression (bar)", "Delta pression (bar)", "Représentation graphique de l'effet d'hystérèse");
        cpgline(200, xr, yr); /* Courbe d'hystérèse */
        getchar();
        cpgclos();
        c = 0;

    } while (c != 0);

    fclose(fptr1);
    fclose(fptr2);
    return 0;
}