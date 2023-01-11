# DEMAGALHAES_Lab2

# Hardware Description
## Architecture
On souhaite implémenter un compteur sur notre DE10-Lite avec les afficheurs 7 segments, pour se faire on commence tout d'abord par décrire l'architecture de notre système.
Il sera composé des éléments suivants : 
* Nios 2
* RAM
* CLK
* Timers
* PIO (7-SEG)
* JTAG

Les élements seront connectés les uns aux autres et décriront l'architecture suivante :
![archESN11_Lab2](https://user-images.githubusercontent.com/77203492/211849922-1e748cab-14b8-405c-8ad7-a970058d76b9.png)


## Platform Designer
Réalisons maintenant cette architecture sur **Platform Designer** :
![image](https://user-images.githubusercontent.com/77203492/211850151-498946b3-c3af-4863-a579-8b6a38cc033e.png)

Sur l'architecture on relie les 3 segments aux **data_master** du nios pour le canal de donnée.
On configure le timer comme suit :

![image](https://user-images.githubusercontent.com/77203492/211850492-edac2dad-f3de-47e0-99fe-5a4d55978765.png)

Pour obtenir notre interruption toutes les secondes, il nous reste à relier la broche **irq** au nios. 

_J'ai ajouté un boutton qui ne sera pas utilisé par la suite malheureusement par manque de temps mais il aurait servi à démarrer le système et à le mettre en pause._

## Top Level design
Passons désormais à la description du design VHDL :
### 7 Segments VHDL
Il faut avant de définir l'architecture global définir le fonctionnement du 7 segments en VHDL. 
Pour chaque code en entrée on aura en sortie un code spécifique pour le 7 segment.
![image](https://user-images.githubusercontent.com/77203492/211851467-ecd0cba6-2166-4d96-b42f-9df4df7b1f1c.png)

### Architecture global
Ensuite il ne reste plus qu'à importer chaque composant dans notre description hardware du système et de réaliser le pin assignement :
![image](https://user-images.githubusercontent.com/77203492/211851892-8c610f76-b532-4c1f-9e45-b9f7ade20043.png)

## Pin Assignement
Sur les 2 images qui suivent vous retrouverez, l'association entre les entrées sorties et les pin physique du FPGA.
![image](https://user-images.githubusercontent.com/77203492/211852135-42ab6ba6-badb-43de-99ad-7ee14a01df7d.png)

# Software Description
Dans cette partie je vais m'attarder sur la description du fonctionnement de chacun des programmes :
## Compteur simple
Le compteur simple comme son nom l'indique, fonctionne par polling où on rentre dans les 7 segments les valeurs.
Pour ce faire on fait appel à la fonction **IOWR_ALTERA_AVALON_PIO_DATA(SEG_BASE,1)** pour écrire sur le 7seg.

## Compteur Final (Interruption du Timer)
Pour le compteur final, on doit définir une routine d'interruption pour le timer, qui vient incrémenter une variable Time toutes les secondes.
On définit l'interruption dans la fonction **handle_timer_interrupt()**
Puis on initialise l'interruption dans le main avec la fonction **alt_ic_isr_register()**
Et on décide de démarrer le compteur seulement au bout de 7secondes, que l'on définit avec : **IOWR_ALTERA_AVALON_TIMER_CONTROL(TIMER_0_BASE, 0x7);**

# Résultat 
Voici une courte vidéo démontrant le principe de fonctionnement :
https://user-images.githubusercontent.com/77203492/211807035-a3131f97-3379-4ab3-bd41-5f9093403c06.mp4


# Conclusion 
Dans ce TP, j'ai bien compris comment créer une interruption avec un timer pour incrémenter une variable de manière périodique, mais aussi comment utiliser une description vhdl pour le 7 segment.
