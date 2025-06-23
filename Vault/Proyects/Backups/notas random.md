- si fallan los fetch, es porque quite el default de useFetch para que se exporte como useFetch en lugar de fetchData


**aca probablemente rompa, getExpirationTime retorna Date supuestamente, pero en caso de que retorne string va a estar mal, asegurarse el tipo de retorno cuando pueda correr esto, la api y ver su valor con typeof. voy a remover la linea const expirationDate = new Date(unmodifiedExpirationTime); ya que si es Date no hace falta convertirla, es redundante.**
![[Pasted image 20250505223008.png]]