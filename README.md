# TP4-Filtrage-Analogique

<a name="retour"></a>
## Sommaire :
1. [ Objectifs. ](#objectif)
2. [ Filtrage et diagramme de Bode.](#part1)
3. [ Dé-bruitage d'un signal sonore. ](#part1)

<a name="objectif"></a>
### **1. Objectifs:**
 Appliquer un filtre réel pour supprimer les composantes indésirables d’un signal
 Améliorer la qualité de filtrage en augmentant l’ordre du filtre.

#### Commentaires :
 Il est à remarquer que ce TP traite en principe des signaux continus. Or, l'utilisation de Matlab suppose l'échantillonnage du signal. Il faudra donc être vigilant par rapport aux différences de traitement entre le temps continu et le temps discret.
 
 #### Tracé des figures :
 toutes les figures devront être tracées avec les axes et les légendes des axes appropriés.
 
 #### Travail demandé :
 un script Matlab commenté contenant le travail réalisé et des commentaires sur ce que vous avez compris et pas compris, ou sur ce qui vous a semblé intéressant ou pas, bref tout commentaire pertinent sur le TP.
 
 $~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$ [ (Revenir au sommaire) ](#retour)
***
 
   <a name="part1"></a>
### **2. Filtrage et diagramme de Bode:**

Sur le réseau électrique, un utilisateur a branché une prise CPL (Courant Porteur en Ligne), les signaux utiles sont de fréquences élevées. Le réseau électrique a cependant sa propre fréquence (50 hz). Le boiter de réception doit donc pouvoir filtrer les basses fréquences pour s'attaquer ensuite à la démodulation du signal utile.

Schématiquement:

<img width="134" alt="a" src="https://user-images.githubusercontent.com/93081417/213943540-585d4362-f424-4e2a-89a8-5ce051084957.png">

Mathématiquement, un tel filtre fournit un signal de sortie en convoluant le signal d'entrée par la réponse temporelle du filtre :

     y(t) = x(t) * h(t)
     
   Nous souhaitons appliquer un filtre passe-haut pour supprimer la composante à 50 Hz. Soit notre signal d'entrée :  

 x(t) = sin(2.pi.f1.t) + sin(2.pi.f2.t) + sin(2.pi.f3.t)
Avec f1 = 500 Hz, f2 = 400 Hz et f3 = 50 Hz

####  **1. Définir le signal x(t) sur t = [0 5] avec Te = 0,0001 s.**

```matlab
% Definition des variables et de signal

te = 1e-4 ;
fe = 1/te ;
t = 0:te:5 ;
N = length(t);
f = (0:N-1)*(fe/N);
fshift = (-N/2:N/2-1)*fe/N;


% Definition de la fonction xt
xt = sin(2*pi*500*t)+ sin(2*pi*400*t)+ sin(2*pi* 50*t) ;
```

#### **2. Tracer le signal x(t) et sa transformé de Fourrier. Qu'observez-vous ?**

```matlab
 y = fft(x); % y: spectre , fft(x) : transformee de fourier
 plot(f,y)
```
<img width="812" alt="c" src="https://user-images.githubusercontent.com/93081417/213944029-4669ba86-2dba-4396-80b5-53d81f2f44ff.png">

La fonction H(f) (transmittance complexe) du filtre passe haut de premier ordre est donnée par :

    H(f) = (K.j.w/wc) / (1 + j. w/wc)

Avec K le gain du signal, w la pulsation et wc la pulsation de coupure. On se propose de tracer le diagramme de Bode de ce filtre et de l'appliquer au signal.

#### **1. Tracer le module de la fonction H(f) avec K=1 et wc = 50 rad/s.**

```matlab
K = 1 ;
wc1 = 50 ;
w = 2*pi*f ; 

H1 = (K*1j*w/wc1)./(1+1j*w/wc1) ;
Module_H1 = abs(H1);
 
   plot(w,Module_H1, 'r')
```

<img width="788" alt="d" src="https://user-images.githubusercontent.com/93081417/214001762-120e8993-1dba-4b3b-9caf-a879d5bebe4b.png">

#### **2. Tracer 20.log(|H(f)|) pour différentes pulsations de coupure wc, qu'observez-vous?**

```matlab

fc1 = 30 ; 
fc2 = 150 ; 
fc3 = 1500;

K = 1 ;  
H1 = (K*1j*f/fc1)./(1+1j*f/fc1) ;
 H2 = (K*1j*f/fc2)./(1+1j*f/fc2) ; 
 H3 =(K*1j*f/fc3)./(1+1j*f/fc3);

%%   
G1 = 20*log(abs(H1));
G2 = 20*log(abs(H2));
G3 = 20*log(abs(H3));   

semilogx(f,G1 , 'g',f,G2,'r' , f,G3, 'b')
```
<img width="786" alt="e" src="https://user-images.githubusercontent.com/93081417/214002933-8b8daddd-75fd-43e6-9e54-8a0b779c472e.png">

Obeservation:
==> le gain a diminué de 3dB par rapport à sa valeur maximum ou par rapport au gain statique suivant la nature du système
