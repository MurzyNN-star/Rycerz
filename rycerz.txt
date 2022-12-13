class Rycerz 
{
    constructor(nowyX, nowyY) 
    {
        //pozycja
        this.x=nowyX;
        this.y=nowyY;

        //siła i masa
        this.fX=0;
        this.fY=0;
        this.masa=10;
        this.tarcie=0.7;

        //przyspieszenie
        this.aX=0;
        this.aY=0;

        //predkosc
        this.vX=0;
        this.vY=0;

        //sterowanie
        this.lewo=false;
        this.prawo=false;
        this.skok=false;
        this.atak=false;
        this.silaRuchu=50;
        this.silaSkoku=600;

        this.aktualnyStan=0;

        //animacja
        this.obrazek=new Image();
        this.obrazek.src="rycerz.png";
        this.szerokoscKlatki=120;
        this.wysokoscKlatki=81;
        this.nrKlatki=0;
        this.skala=5;

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
        this.fX=0;
        this.fY=0;
        this.dodajSile(0, grawitacja);
        this.steruj();
        this.obliczPrzyspieszenie();
        this.obliczPredkosc();
        this.obliczPozycje();
        this.obliczHitbox();
        this.sprawdzKolizje();
        this.narysuj();
        this.debuguj();
    }

    steruj() 
    {
        if ( this.lewo ) this.dodajSile(-this.silaRuchu,0);
        if ( this.prawo ) this.dodajSile(this.silaRuchu,0);
        if ( this.skok && this.kolizjaD) {
            this.dodajSile(0,-this.silaSkoku);
        }
        if ( this.atak ) this.aktualnyStan=7;
    }

    dodajSile(silaX, silaY) 
    {
        this.fX+=silaX;
        this.fY+=silaY;
    }

    obliczPrzyspieszenie() 
    {
        this.aX=this.fX/this.masa;
        this.aY=this.fY/this.masa;
    }

    obliczPredkosc() 
    {
        this.vX+=this.aX;
        this.vY+=this.aY;

        //jeżeli wartość absolutna prędkości jest mniejsza niż 0.1 piksela, zatrzymaj się
        if (Math.abs(this.vX)<0.1) this.vX=0;
        if (Math.abs(this.vY)<0.1) this.vY=0;

        this.vX*=this.tarcie;
    }

    obliczPozycje() 
    {
        this.x+=this.vX;
        this.y+=this.vY;
    }

    obliczHitbox() 
    {
        //hitbox jest inny, niż sam rysunek, więc deklarujemy go osobno
        this.hitboxL=this.x + (43 * this.skala);
        this.hitboxP=this.x+this.szerokoscKlatki*this.skala - (56 * this.skala);
        this.hitboxG=this.y + (43 * this.skala);
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
        let rysujX=this.x;
        if (this.vX<0) 
        {    
            //jeżeli poruszam się w lewo
            rysujX=-this.x-this.szerokoscKlatki*this.skala;
            kontekst.scale(-1,1);  //odwracamy układ współrzędnych
        }

        kontekst.drawImage
        (
            //obraz do narysowania
            this.obrazek,
            //lewy górny róg na obrazku
            this.szerokoscKlatki*Math.round(this.nrKlatki), this.aktualnyStan*this.wysokoscKlatki,
            //wysokość i szerokość na obrazku
            this.szerokoscKlatki, this.wysokoscKlatki,
            //lewy górny róg na płótnie
            rysujX, this.y,
            //wysokość i szerokość na płótnie
            this.szerokoscKlatki*this.skala, this.wysokoscKlatki*this.skala
        ); 
        
        if (this.vX<0) kontekst.scale(-1,1);  //obracamy świat z powrotem
        
        //przejdź do kolejnej klatki
        this.nrKlatki+=0.6;
        if (this.aktualnyStan==2) 
        {
            if (this.nrKlatki>7) this.nrKlatki=0;
        } else {
            if (this.nrKlatki>9) this.nrKlatki=0;
        }
    }

    narysujHitboxy() 
    {
        kontekst.beginPath();
        kontekst.strokeStyle="black";
        kontekst.rect(this.hitboxL, this.hitboxG, this.hitboxP-this.hitboxL, this.hitboxD-this.hitboxG);
        kontekst.stroke();
    }

    debuguj() 
    {
        var tekst="";
        tekst+="lewo: "+this.lewo+"<br>";
        tekst+="prawo: "+this.prawo+"<br>";
        tekst+="skok: "+this.skok+"<br>";
        tekst+="atak: "+this.atak+"<br>";

        tekst+="X: "+this.x+"<br>";
        tekst+="Y: "+this.y+"<br>";

        tekst+="vX: "+this.vX+"<br>";
        tekst+="vY: "+this.vY+"<br>";

        tekst+="aX: "+this.aX+"<br>";
        tekst+="aY: "+this.aY+"<br>";

        tekst+="fX: "+this.fX+"<br>";
        tekst+="fY: "+this.fY+"<br>";

        debugBox.style.top=this.hitboxG+"px";
        debugBox.style.left=(this.hitboxP-100)+"px";
        this.narysujHitboxy();
    }

    interakcjazpudelkiem(pudelko)
    {
        if (this.hitboxP>pudelko.hitboxP && this.hitboxL<pudelko.hitboxL && this.hitboxD>pudelko.hitboxG && (this.aktualnyStan==2 || this.aktualnyStan == 7))
        { 
            this.y = this.y -12; 
            this.obliczHitbox();
            this.vY = 0; 
        }
        else if ((this.hitboxP+25)>pudelko.hitboxP && (this.hitboxL+25)<pudelko.hitboxL && this.hitboxD>pudelko.hitboxG)
        {
            this.x = this.x -15; 
            this.obliczHitbox();
            this.vX = 0;
        }
        else if ((this.hitboxP-25)>pudelko.hitboxP && (this.hitboxL-25)<pudelko.hitboxL && this.hitboxD>pudelko.hitboxG)
        {
            this.x = this.x +15;
            this.obliczHitbox();
            this.vX = 0;
        }
        
        

    }
}