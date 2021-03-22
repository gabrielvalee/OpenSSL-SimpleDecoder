# Simple OpenSSL decoder

This is a OpenSSL decoder developed in python for the class of "information security audit" in the "Federal University of Uberlandia".

What was requested in the project:
 Someone encrypted 15 files using a strong encryptor (aes/256 bits), the password may contain words (upper and lowercase), numbers, exclamation point(!) and at sign(@). You know whoever encrypted the files likes "La casa de papel" a drama television series. You must find the passwords for the files.

In order to decode the files I created a .txt file named **"base-dict.txt"** that contains a lot of key words and names about "La casa de papel".
A file named **"nome-arq.txt"** was created to include the names of the files that would be decoded.
Finally the code was written in python and deveoped to work on linux. The final file is **"Decifrador.py"**

```python
import subprocess

def main():
	arquivoDict = open("base-dict.txt", "r")
	dictOriginal = arquivoDict.read().split("\n") #Getting the original words
	dictOriginal.remove("")
	arquivoDict.close()
	
	arquivoArq = open("nome-arq.txt", "r")
	arqNomes = arquivoArq.read().split("\n") #Getting the names of the files to decode 
	arqNomes.remove("")
	arquivoArq.close()
	
	senhas = open("senhas-descobertas.txt", "a") #Creating the file with the discovered passwords
  
	nModifica = 13                        #Number of modifiers
	cont = 0                              #To know what modifier to use
	arquivo = 0                           #To know which file to try to decode
	retorno = 1                           #variable to get the return of a function
	dictCopia = dictOriginal.copy()       #copying the original words
	
	while arquivo < len(arqNomes):
		palavra = 0
	
		while palavra < len(dictCopia): #tentativas para decifrar
			retorno = subprocess.call(["openssl", "enc", "-d", "-aes-256-cbc", "-pbkdf2", "-salt", "-in", arqNomes[arquivo], "-out", "arquivo" + str(arquivo) + "decifrado.txt", "-pass", "pass:" + dictCopia[palavra]])
			if retorno == 0: #managed to decode
				print("Successfully decoded with the password: " + dictCopia[palavra])
				print("\n")
				senhas.write("File: " + arqNomes[arquivo] + " | Password: " + dictCopia[palavra] + "\n")
				arquivo += 1
				cont = 0
				dictCopia = dictOriginal.copy()
				break
			elif retorno == 1: #failed to decode
				print("Error for the password: " + dictCopia[palavra])
				palavra += 1
		else: #Modify the words
			cont += 1
			if cont <= nModifica:
				dictCopia = modificaPalavra(dictOriginal,cont)
			else:
				print("Failed to decode file:  " + arqNomes[arquivo])
				arquivo += 1
				cont = 0
				dictCopia = dictOriginal.copy()

	senhas.close()
					
def modificaPalavra(listPalavras, numMod):	
			
			if numMod == 1: #capitalize all words
				listaMod = [palavra.capitalize() for palavra in listPalavras]
				
			elif numMod == 2: #Uppercase all words
				listaMod = [palavra.upper() for palavra in listPalavras]
				
			elif numMod == 3: #Add ! at the end of all words
				listaMod = [(palavra + "!") for palavra in listPalavras]
			
			elif numMod == 4: #Add @ at the end of all words
				listaMod = [(palavra + "@") for palavra in listPalavras]
				
			elif numMod == 5: #Replace a with @
				listaMod = [palavra.replace("a","@") for palavra in listPalavras]
				
			elif numMod == 6: #Replace i with !
				listaMod = [palavra.replace("i","!") for palavra in listPalavras]
				
			elif numMod == 7: #Replace o with 0
				listaMod = [palavra.replace("o","0") for palavra in listPalavras]
				
			elif numMod == 8: ##Replace l with 1
				listaMod = [palavra.replace("l","1") for palavra in listPalavras]
				
			elif numMod == 9: #Replace e with 3
				listaMod = [palavra.replace("e","3") for palavra in listPalavras]
				
			elif numMod == 10: #Replace a with 4
				listaMod = [palavra.replace("a","4") for palavra in listPalavras]

			elif numMod == 11: #Replace t with 7
				listaMod = [palavra.replace("t","7") for palavra in listPalavras]
			
			elif numMod == 12: #Replace s with 5
				listaMod = [palavra.replace("s","5") for palavra in listPalavras]
            
      			elif numMod == 13: #Replace i with 1
				listaMod = [palavra.replace("i","1") for palavra in listPalavras]
			
			return listaMod
				

if __name__ == "__main__":
    main()
```

The code will:
 1. Get the files and words
 2. Try all words for a file and modify then to try again until find the correct one or until try every word.
 3. Change to the next file and try again.

`Openssl` is used to decode the files with the following arguments `enc -d -aes-256-cbc -pbkdf2 -salt -in file.enc -out decoded.txt -pass pass:Wordtotry` with `file.enc` being the file that will be decoded, `decoded.txt` being the file decoded and `Wordtotry` being the password.
