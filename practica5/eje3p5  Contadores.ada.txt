task type contador;
task administrador;

task administrador is
	entry get_numero(n : out integer);
	entry give_cantidad(cant : in integer);
end administrador;

task body administrador is
	I,cant,total,n : integer;
	total := 0;
	decidir_numero(n);
	for I in 1..N loop 
		accept get_numero(n);
	end loop;
	for I in 1..N loop
		accept give_cantidad(cant);
		total := total + cant;
	end loop;
end administrador;

task body contador is
	A : array (1..X) of Integer;
	temp : Integer;
	administrador.get_numero(n);
	for I in 1..X loop
		if (A(I)=n) then
			temp := temp + 1;
	end loop;
	administrador.give_cantidad(temp);
end contador;
		