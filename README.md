# CHATSEC - Secure Chatroom

CHATSEC est une application de messagerie sécurisée en Python. La version actuelle du projet n'utilise plus RabbitMQ ni LDAP pour le flux principal : elle repose sur un serveur TCP/TLS local, une base SQLite et une interface client Tkinter remise au propre pour un usage sous Windows 11.

## Fonctionnalités

- Inscription et connexion utilisateur via le serveur local.
- Stockage des comptes dans SQLite avec hash de mot de passe `bcrypt`.
- Transport client/serveur en TLS avec certificat auto-signé.
- Messages privés chiffrés côté client avec RSA.
- Liste des utilisateurs connectés en temps réel.
- Interface client compatible Windows 11 : écrans connexion/inscription, thème sombre, zone de chat, menu, sauvegarde du journal et réglage de la taille de fenêtre.

## Interface Windows 11

L'interface principale est lancée par `main.py`. Elle conserve le parcours classique du projet, mais avec une présentation plus moderne et plus claire :

- splash screen au démarrage ;
- écran de connexion séparé ;
- écran d'inscription séparé ;
- interface de chat avec liste des utilisateurs connectés à gauche ;
- zone de conversation centrale ;
- champ de saisie et bouton d'envoi visibles en bas ;
- menus pour sauvegarder le journal, effacer la conversation, changer le thème, changer la police et gérer la taille de fenêtre.

Le thème par défaut est sombre pour mieux coller à l'esthétique Windows 11 et rester lisible pendant les tests.

## Architecture

```mermaid
graph TD
    subgraph Client Tkinter [Client Tkinter Windows 11]
        M[main.py<br/>Splash Screen] -->|Lance| Auth[login.py / signup.py<br/>Authentification]
        Auth -->|Succès| C[chat.py<br/>Logique Métier]
        C <--> I[interface.py<br/>UI Tkinter]
        C <--> Net[chatsec_client.py<br/>Gestion Réseau]
        C <--> Crypto[encryption_decryption.py<br/>RSA-OAEP]
    end

    subgraph Connexion [Réseau - Port 5555]
        Net <== "TLS + JSON" ==> Serv
    end

    subgraph Serveur [Serveur Central]
        Serv[server.py<br/>Routing] <--> DB[database.py]
        DB <--> SQLite[(chatsec.db<br/>SQLite)]
    end
    
    classDef client fill:#e3f2fd,stroke:#1565c0;
    classDef server fill:#fff3e0,stroke:#e65100;
    class Client Tkinter client;
    class Serveur server;
