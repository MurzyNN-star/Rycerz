var plotno;
var kontekst;
var gracz;
var debugBox;
var grawitacja=45;
var przeszkody=[];

window.onload=zaladuj;

function zaladuj() 
{
    plotno=document.getElementById('mojePlotno');
    kontekst=plotno.getContext('2d');
    naciagnijPlotno();

    debugBox=document.getElementById('debugBox');

    window.addEventListener("resize", naciagnijPlotno);
    window.addEventListener("keydown", wcisnietyPrzycisk);
    window.addEventListener("keyup", puszczonyPrzycisk);

    gracz=new Rycerz(200,50);
    przeszkody[0]=new Przeszkoda(1000,750, 100,200);
    setInterval(klatka,1000/50);
}

function naciagnijPlotno() 
{
    plotno.width=document.body.clientWidth;
    plotno.height=document.body.clientHeight;
}

function wcisnietyPrzycisk(zdarzenie) 
{
    if (zdarzenie.key=="a") gracz.lewo=true;
    if (zdarzenie.key=="d") gracz.prawo=true;
    if (zdarzenie.key=="w") gracz.skok=true;
    if (zdarzenie.key=="z") gracz.atak=true;
}

function puszczonyPrzycisk(zdarzenie) 
{
    if (zdarzenie.key=="a") gracz.lewo=false;
    if (zdarzenie.key=="d") gracz.prawo=false;
    if (zdarzenie.key=="w") gracz.skok=false;
    if (zdarzenie.key=="z") gracz.atak=false;
}

function klatka() 
{
    kontekst.clearRect(0, 0, plotno.width, plotno.height);
    for (let i=0; i<przeszkody.length; i++) 
    {
        przeszkody[i].klatka();
        gracz.interakcjazpudelkiem(przeszkody[i])
    }
    gracz.klatka();
}