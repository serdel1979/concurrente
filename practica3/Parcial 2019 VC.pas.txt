//1. Resolver el problema de productor consumidor para un buffer de N elementos, 
//con 1 consumidor y 1 productor. Usando sentencias <> y <await B;S>
Program PC
begin
queue elementos;
cant := 0;
procedure productor
while (true)
	producir(e);
	<await (cant < N); cant++; elementos.push(e)>

procedure consumidor
while (true)
	<await (cant > 0); cant--; elementos.pop(e)>
	consumir(e);
end;

//2. Semaforos 
//N personas quieren usar una maquina pero solo puede usarla uno a la vez
//En caso de que la maquina este ocupada hacen cola y la usan en orden de llegada

//Esto es un passing the baton puro
Program semaforos

var 

	queue cola;
	sem[1..N] dormidos := ([N]0);
	boolean libre := true;
	sem mutex_libre := 1; mutex_cola := 1;

procedure persona[p:1..N]
begin
	P(mutex_libre)
	if (!libre) //Me duermo
	begin
		V(mutex_libre);
		P(mutex_cola);
		cola.push(p);
		V(mutex_cola);
		P(dormidos[p]);
	end
	else
	begin //Tomo el baton
		libre := false;
		V(mutex_libre);
	end;
	delay(usandoMaquina(p));
	P(mutex_cola);
	if (cola.isEmpty()) then
	begin //Libero el baton
		P(mutex_libre);
		libre := true;
		V(mutex_libre);
		V(mutex_cola);
	end 
	else //Le paso el baton a quien corresponde
	begin
		cola.pop(p);
		V(dormidos[p]);
		V(mutex_cola);		
	end;
		
end;

//3. Monitores
//Una empresa tiene 40 trabajadores y 4 combis.
//Cada trabajador conoce su equipo y cada equipo tiene su combi
//Los trabajadores suben a la combi de su equipo
//Cuando las 4 combi estan listas inician el viaje hacia diferentes plantas
//El viaje dura diferente tiempo para cada combi (entre 15 y 20 minutos)
//Apenas llega la combi a su planta los trabajadores bajan y comienzan a trabajar.
//Los empleados se retiran por separado apenas terminan su tarea

procedure persona[p:1..40]

begin
	equipo := getEquipo(p);
	estado_combi[equipo].subir();
	estado_combi[equipo].llegamos();
	delay(tiempoDeTrabajar(p));
	retirarse();
end; 


procedure combi[c:1..4]

begin
	estado_combi[c].subir();
	delay(random_btw(15,20));
	estado_combi[c].llegamos();
end;


MONITOR estado_combi[m:1..4]
var
	cond cv;
	syn1 := 0; //Usados para sincronizar que todos los procesos esten listos
	syn2 := 0; //antes de continuar
begin


	process subir()
	begin
		syn1++
		if (syn1 < 11) then
			wait(cv)
		else
			syn.sincronizar_salida();
			signalall(cv);
		
	end;
	
	process llegamos()
	begin
		syn2++
		if (syn2 < 11) then
			wait(cv)
		else
			signalall(cv);	
	end;
end;

MONITOR syn
var
	cond cv;
	cont := 0;
begin
	process sincronizar_salida()
	begin
		cont++
		if (cont < 4) then
			wait(cv)
		else
			signalall(cv);
	end;
end;




