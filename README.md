==========================================================================

                       P R O T E U S   A D D O N S

==========================================================================

  TICKET SYSTEM
  Système de tickets de support en jeu pour serveurs de jeu Garry's Mod.

   Plateforme   :  Garry's Mod
   Catégorie    :  Administration / Support
   Version      :  1.0.0
   Auteur       :  Proteus
   Licence      :  FREE

==========================================================================


--------------------------------------------------------------------------
  PRÉSENTATION
--------------------------------------------------------------------------

Les joueurs ouvrent un ticket de support via une commande chat. Le staff
les gère depuis un panel VGUI : prise en charge, téléportation, réponses
et fermeture. Les tickets sont sauvegardés sur disque, journalisés, et
peuvent déclencher une alerte Discord via webhook.

L'addon est autonome et ne nécessite aucune dépendance externe.


--------------------------------------------------------------------------
  PUBLIC VISÉ
--------------------------------------------------------------------------

Créateurs et administrateurs de serveurs Garry's Mod (DarkRP, sandbox,
serveurs RP ou communautaires) qui veulent un système de support en jeu
simple, autonome et configurable.


--------------------------------------------------------------------------
  INSTALLATION
--------------------------------------------------------------------------

1. Placer le dossier "proteus_ticketsystem" dans :

      garrysmod/addons/

2. Redémarrer le serveur (ou changer de map).

L'addon se charge automatiquement sur le serveur et les clients.


--------------------------------------------------------------------------
  COMMANDES JOUEUR
--------------------------------------------------------------------------

  !ticket <message>      Ouvre un nouveau ticket avec le message indiqué.
  !ticket                Affiche le statut de ton ticket en cours.
  !ticket reply <texte>  Ajoute une réponse ou une précision au ticket.
  !ticket close          Ferme ton propre ticket.

Un joueur ne peut avoir qu'un seul ticket actif à la fois (configurable).
Un cooldown empêche l'ouverture de tickets en rafale.


--------------------------------------------------------------------------
  COMMANDE STAFF
--------------------------------------------------------------------------

  !tickets               Ouvre le panel d'administration des tickets.

Le panel affiche la liste des tickets (ID, joueur, statut, âge) et un
volet de détail avec les actions suivantes :

  Prendre    Prend le ticket en charge (statut "Pris en charge").
  Aller      Te téléporte vers le joueur du ticket.
  Amener     Téléporte le joueur du ticket vers toi.
  Répondre   Envoie une réponse au joueur (champ texte du volet).
  Fermer     Ferme le ticket. Le champ texte sert de motif de fermeture.

Le champ texte du volet de détail sert à la fois pour la réponse et pour
le motif de fermeture. Une réponse du staff sur un ticket encore "Ouvert"
le passe automatiquement en "Pris en charge".


--------------------------------------------------------------------------
  PERMISSIONS
--------------------------------------------------------------------------

Un joueur est considéré comme staff si son groupe figure dans
Config.AdminGroups, ou (si Config.AllowIsAdminFallback est actif) si la
fonction IsAdmin() renvoie vrai.

Groupes autorisés par défaut : admin, superadmin, moderator.
Modifiable dans le fichier de configuration.


--------------------------------------------------------------------------
  CONFIGURATION
--------------------------------------------------------------------------

Tout se règle dans :

      lua/proteus_tickets/sh_config.lua

Options principales :

  PlayerCommand / AdminCommand
      Les commandes chat. Par défaut !ticket et !tickets.

  CooldownSeconds
      Délai minimum entre deux ouvertures de ticket par un même joueur.

  MaxMessageLength / MaxReplyLength
      Longueur maximale d'un message et d'une réponse.

  OnePerPlayer
      Si vrai, un joueur ne peut avoir qu'un ticket actif à la fois.

  AdminGroups / AllowIsAdminFallback
      Définition des groupes staff (voir section PERMISSIONS).

  RebootResetThreshold
      Nombre de démarrages avant la remise à zéro (voir section LOGS).

  NotifySound
      Son joué côté staff à l'arrivée d'un nouveau ticket.


--------------------------------------------------------------------------
  PERSISTANCE, LOGS ET REMISE À ZÉRO
--------------------------------------------------------------------------

Les tickets sont sauvegardés sur disque et survivent aux redémarrages du
serveur. À chaque démarrage, l'addon recharge les tickets en cours.

Un fichier de logs accumule en continu les évènements (création, prise en
charge, réponses, fermetures) ainsi que les tickets encore en attente au
moment de l'arrêt du serveur.

Tous les RebootResetThreshold démarrages (4 par défaut), une remise à zéro
complète est effectuée :
  - le fichier de logs est vidé et repart à neuf ;
  - les tickets pris en charge et fermés sont supprimés ;
  - seuls les tickets encore "Ouvert" (non traités) sont conservés ;
  - le compteur de reboot repart à zéro.

Cela évite que le fichier de logs et la base de tickets ne grossissent
indéfiniment, tout en ne perdant jamais un ticket non traité.


--------------------------------------------------------------------------
  WEBHOOK DISCORD (OPTIONNEL)
--------------------------------------------------------------------------

L'addon peut fprévenir le staf sur Discord via un webhook.

Fonction principale : lorsqu'un ticket reste "Ouvert" (non pris en charge)
pendant plus que StaleSeconds (300s, soit 5 min, par défaut), un message
embed est envoyé sur le webhook avec la nature du ticket et une demande
explicite qu'un staff y réponde.

Configuration dans Config.Webhook (sh_config.lua) :

  Enabled
      Active ou désactive entièrement le webhook. Faux par défaut.

  Url
      L'URL du webhook Discord. À renseigner pour activer l'envoi.

  Username
      Nom affiché par le webhook sur Discord.

  StaleSeconds
      Durée sans réponse au-delà de laquelle un ticket "Ouvert" déclenche
      une alerte webhook. 300 par défaut.

  CheckInterval
      Fréquence de vérification des tickets en attente, en secondes.

  RenotifyInterval
      Si supérieur à 0, renvoie une alerte tous les N secondes tant que le
      ticket reste sans réponse. À 0, une seule alerte est envoyée.

  NotifyOnCreate
      Si vrai, envoie aussi un message à chaque nouveau ticket.

Pour activer : mettre Enabled à true, coller l'URL du webhook dans Url.


--------------------------------------------------------------------------
  FICHIERS DE DONNÉES
--------------------------------------------------------------------------

Créés automatiquement par l'addon dans :

      garrysmod/data/proteus_tickets/

  active.json    Tickets en cours, rechargés au démarrage.
  logs.txt       Journal cumulatif des évènements.
  meta.json      Compteur de redémarrages.


--------------------------------------------------------------------------
  STRUCTURE DE L'ADDON
--------------------------------------------------------------------------

  proteus_ticketsystem/
    Readme.txt
    lua/
      autorun/
        proteus_tickets_loader.lua    Chargeur (deux réalités)
      proteus_tickets/
        sh_config.lua                 Configuration partagée
        sh_util.lua                   Fonctions utilitaires partagées
        sv_storage.lua                Stockage, logs, gestion reboot
        sv_tickets.lua                Logique métier des tickets
        sv_webhook.lua                Intégration webhook Discord
        sv_net.lua                    Réseau et actions staff
        sv_chat.lua                   Commandes chat
        cl_notify.lua                 Notifications chat client
        cl_panel.lua                  Panel VGUI de gestion


--------------------------------------------------------------------------
