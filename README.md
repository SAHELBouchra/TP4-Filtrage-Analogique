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

#### **3. Tracer le module de la fonction H(f) avec K=1 et wc = 50 rad/s.**

```matlab
K = 1 ;
wc1 = 50 ;
w = 2*pi*f ; 

H1 = (K*1j*w/wc1)./(1+1j*w/wc1) ;
Module_H1 = abs(H1);
 
   plot(w,Module_H1, 'r')
```

<img width="788" alt="d" src="https://user-images.githubusercontent.com/93081417/214001762-120e8993-1dba-4b3b-9caf-a879d5bebe4b.png">

#### **4. Tracer 20.log(|H(f)|) pour différentes pulsations de coupure wc, qu'observez-vous **

dans ce cas on travaille avec fc:

```matlab

fc1 = 30 ; 
fc2 = 150 ; 
fc3 = 1500;

K = 1 ;  
H1 = (K*1j*f/fc1)./(1+1j*f/fc1) ;
 H2 = (K*1j*f/fc2)./(1+1j*f/fc2) ; 
 H3 =(K*1j*f/fc3)./(1+1j*f/fc3);

% diagramme de bode en fct de la phase
P1 = angle(H1);
P2 = angle(H2);
P3 = angle(H3); 

%%   
G1 = 20*log(abs(H1));
G2 = 20*log(abs(H2));
G3 = 20*log(abs(H3));   

semilogx(f,G1 , 'g',f,G2,'r' , f,G3, 'b')
```
<img width="786" alt="e" src="https://user-images.githubusercontent.com/93081417/214002933-8b8daddd-75fd-43e6-9e54-8a0b779c472e.png">

Obeservation:

==> Plus la pulsation de coupure est petite, plus le filtre est passe haut et la transmittance sera plus élevée pour les fréquences élevées.

#### **Choisissez wc qui vous semble optimal. Le filtre est-il bien choisi ? Pourquoi ?**
 
             ==>fc qui semble optimal est 1000hz
             
```matlab
yt1 = y.*H1 ;
Yt1 =ifft(yt1,'symmetric'); 
yyt1=fft(Yt1)
plot(fshift,fftshift(abs(yyt1)/N)*2
```
 <img width="782" alt="po" src="https://user-images.githubusercontent.com/93081417/215321272-a174c492-7a55-44ef-8001-2a5e0f748c43.png">


#### **Observez le signal y(t) obtenu, puis Comparer-le avec le signal que vous auriez souhaité obtenir. Conclusions ?**

    ==>Un filtre passe-haut ne peut pas supprimer complètement toutes les informations indésirables car il ne peut sélectionner qu'une plage de fréquences           élevées.
        
$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$ [ (Revenir au sommaire) ](#retour)
***



 
   <a name="part2"></a>
### **3. Dé-bruitage d'un signal sonore:**

Dans son petit studio du CROUS, un mauvais futur ingénieur a enregistré une musique en
« .wav » avec un très vieux micro. Le résultat est peu concluant, un bruit strident s'est ajouté à sa musique. Heureusement son voisin, expert en traitement du signal est là pour le secourir :

« C'est un bruit très haute fréquence, il suffit de le supprimer. » dit-il sûr de lui.

#### **1. Proposer une méthode pour supprimer ce bruit sur le signal**

```matlab
clear all
close all
clc

[music, fs] = audioread('test.wav');
sound(music,fs);
music = music';
N=length(music);
te = 1/fs;
t = (0:N-1)*te;
N = length(music);

f = (0:N-1)*(fs/N);
fshift = (-N/2:N/2-1)*(fs/N);

spectre_music = fft(music);
subplot(2,1,1)
plot(t, music)
 title('le signal de musique :');
subplot(2,1,2)
plot(fshift,fftshift(abs(spectre_music)));
title('le spectre de musique:');

```
<img width="808" alt="io" src="https://user-images.githubusercontent.com/93081417/215321795-f40b30ec-c5c3-4a5c-9c50-d273bf90f165.png">


#### **2. Mettez-la en oeuvre. Quelle influence à le paramètre K du filtre que vous avez utilisé ?**
##### ~Pour K = 1:

```matlab
k = 1;
fc = 4500;
%la transmitance complexe 
h = k./(1+1j*(f/fc));

h_filter = [h(1:floor(N/2)), flip(h(1:floor(N/2)))];
y_filtr = spectre_music(1:end-1).*h_filter;
sig_filtred= ifft(y_filtr,"symmetric");
semilogx(f(1:floor(N/2)),abs( h(1:floor(N/2))),'linewidth',1.5)
title('le gain :');
```
<img width="376" alt="lp" src="https://user-images.githubusercontent.com/93081417/215321961-c5cdb4ee-bc2e-4e1f-99c3-bbd74ecbc057.png">

```matlab
plot(fshift(1:end-1),fftshift(abs(fft(sig_filtred))));
% sound(sig_filtred,fs);
title('le spectre du signal filtré ');
```

<img width="388" alt="lk" src="https://user-images.githubusercontent.com/93081417/215322001-702484c1-cbd2-4fb5-be8e-41471182282e.png">

##### ~Pour K = 10:

<img width="374" alt="k" src="https://user-images.githubusercontent.com/93081417/215322126-1dab3ba5-d75b-4e31-83dc-42c7f80cd7a2.png">

<img width="388" alt="l" src="https://user-images.githubusercontent.com/93081417/215322148-a61b3e3d-ebb7-44cf-b645-4dd69427d45d.png">


#### ==>Explication:
  Plus le paramètre K est élevé, plus l'amplitude des fréquences autour de la fréquence de coupure sera élevée.

#### **Améliorer la qualité de filtrage en augmentant l’ordre du filtre**

```matlab
k = 10;
fc = 4500;
%la transmitance complexe 
h = k./(1+1j*(f/fc).^100); //Order=100

h_filter = [h(1:floor(N/2)), flip(h(1:floor(N/2)))];
y_filtr = spectre_music(1:end-1).*h_filter;
sig_filtred= ifft(y_filtr,"symmetric");
semilogx(f(1:floor(N/2)),abs( h(1:floor(N/2))),'linewidth',1.5)
title('le gain ');
```
<img width="364" alt="s" src="https://user-images.githubusercontent.com/93081417/215322371-7c932a0c-99a5-4cb6-81b2-9e1c8f583b68.png">

<img width="371" alt="Capture d’écran 2023-01-29 121502" src="https://user-images.githubusercontent.com/93081417/215322410-0cd133e8-e59d-4a22-8142-d92128b53ce9.png">


$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$ [ (Revenir au sommaire) ](#retour)
***

## Réalisé par : SAHEL Bouchra
## Filiére : Robotique et Cobotique .
## Encadré par : Pr. Ammour Alae .



