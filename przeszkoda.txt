class Przeszkoda {
    constructor(nowyX, nowyY, szerokosc, wysokosc) 
    {
        //pozycja
        this.x=nowyX;
        this.y=nowyY;

        this.wysokoscKlatki = wysokosc;
        this.szerokoscKlatki = szerokosc;
        this.skala = 1;
        //animacja
        this.obrazek=new Image();
        
        this.obrazek.src="przeszkoda.png";
        

        //Hitbox
        this.hitboxL=this.x; 
        this.hitboxP=this.x+this.szerokoscKlatki*this.skala;
        this.hitboxG=this.y;
        this.hitboxD=this.y+this.wysokoscKlatki*this.skala;

        //Kolizje
        this.kolizjaL=false;
        this.kolizjaP=false;
        this.kolizjaG=false;
        this.kolizjaD=false;
    }

    klatka()    
    {
        this.obliczHitbox();
        this.sprawdzKolizje();
        this.narysuj();
        this.debuguj();
    }

    obliczHitbox() 
    {
        //hitbox jest inny, niż sam rysunek, więc deklarujemy go osobno
        this.hitboxL=this.x + (this.szerokoscKlatki * this.skala);
        this.hitboxP=this.x+this.szerokoscKlatki*this.skala - (this.szerokoscKlatki * this.skala);
        this.hitboxG=this.y + this.wysokoscKlatki*this.skala-(this.wysokoscKlatki * this.skala);
        this.hitboxD=this.y+this.wysokoscKlatki*this.skala;

        //po obróceniu obrazka, hitbox musimy przesunąć 
        if (this.vX<0) {
            this.hitboxL+=(10 * this.skala);
            this.hitboxP+=(10 * this.skala);
        }
    }

    sprawdzKolizje() 
    {
        //atak jest najważniejszą animacją, więc wszelkie zmiany w animacji
        //sprawdzają, czy przypadkiem nie atakujemy
        if (!this.atak) this.aktualnyStan=2;

        //kolizja ze spodem ekranu
        if (this.hitboxD>=mojePlotno.height) 
        {
            this.kolizjaD=true;
            //przesuwamy gracza tak, aby jego dolna krawędź
            //dotykała dołu ekranu
            this.y=mojePlotno.height-this.wysokoscKlatki*this.skala;
            //resetujemy prędkość w pionie
            this.vY=0;
            //przesunęliśmy gracza, więc obliczamy na nowo hitbox
            this.obliczHitbox();
        } 
        else 
        {
            this.kolizjaD=false;
        }

        if (this.kolizjaD && !this.atak) 
        {
            this.aktualnyStan=0;
            if (this.lewo || this.prawo) this.aktualnyStan=1;
        }
    }

    narysuj() 
    {
        kontekst.drawImage
        (
            //obraz do narysowania
            this.obrazek,
            //lewy górny róg na obrazku
            this.szerokoscKlatki, this.wysokoscKlatki,
            //wysokość i szerokość na obrazku
            this.szerokoscKlatki, this.wysokoscKlatki,
            //lewy górny róg na płótnie
            this.x, this.y,
            //wysokość i szerokość na płótnie
            this.szerokoscKlatki*this.skala, this.wysokoscKlatki*this.skala
        ); 
    }

    narysujHitboxy() 
    {
        kontekst.beginPath();
        kontekst.strokeStyle="red";
        kontekst.rect(this.hitboxL, this.hitboxG, this.hitboxP-this.hitboxL, this.hitboxD-this.hitboxG);
        kontekst.stroke();
    }

    debuguj() 
    {
        var tekst="";

        tekst+="X: "+this.x+"<br>";
        tekst+="Y: "+this.y+"<br>";
        debugBox.style.top=this.hitboxG+"px";
        debugBox.style.left=(this.hitboxP-100)+"px";
        this.narysujHitboxy();
    }

}

