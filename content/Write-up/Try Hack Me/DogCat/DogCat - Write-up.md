Enumeração de serviços:
![[Pasted image 20240131144723.png]]

Identificação do uso de include no parâmetro view. LFI utilizando filter para carregar o arquivo PHP que carrega as imagens.
No código é possível identificar dois requisitos na URL, a palavra cat ou dog e uso de ext, dessa forma identificamos o log do apache e evoluímos pra uma RCE.
Com a shell é possível identificar a segunda flag e abusando do binário env escalamos e pegamos a terceira.
Após analise do script em /opt, descobrimos que é utilizado pela máquina hospedeira do container, então alteramos o script para uma shell reversa e pegamos a quarta flag.