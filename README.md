 
 from random import randint


def mostrar_matriz(matriz):

   cont = 1

   for c in matriz:

       print(f'{cont}- [{c[0][-1]},', end='')

       for d in range(1, 8):

           print(f' {c[d][-1]},', end='')

       print(f' {c[8][-1]}]')

       cont += 1

   print(" "*4, end="")

   for c in range(1, 10):

       print(f"^{c}", end=" ")

   print()



def mostrar_matriz_debug(matriz):

   # Essa função mostra a resposta da matriz

   for c in matriz:

       print(f'[{c[0][0]},', end='')

       for d in range(1, 8):

           print(f' {c[d][0]},', end='')

       print(f' {c[8][0]}]')



def mostrar_area_complexa(matriz):

   while True:

       edit = False

       for a in range(0, 9):

           for b in range(0, 9):

               for c in range(a-1 if a>0 else 0, a+2 if a<8 else 9):

                   if len(matriz[a][b]) == 1: continue

                   mostrar = False

                   for d in range(b-1 if b>0 else 0, b+2 if b<8 else 9):

                       if matriz[c][d][0] == '0' and len(matriz[c][d]) == 1:

                           matriz[a][b].pop(1)

                           mostrar = edit = True

                           break

                   if mostrar: break

       if not edit: return matriz



# De acordo com o jogo original, temos 13 bombas em 81 espaços #

def inserir_bombas(matriz):

   for c in range(0, 13):

       # Garante que serão 13 valores (não será colocada bomba onde já existe)

       while True:

           x = randint(0, 8)

           y = randint(0, 8)

           if matriz[x][y][0] != '●':

               matriz[x][y][0] = '●'

               break

   return matriz



def calcular_minas_adjacentes(matriz):

   for a in range(0, 9):

       for b in range(0, 9):

           if matriz[a][b][0] == '●': continue

           cont_minas_adj = 0

           for c in range(a-1 if a>0 else 0, a+2 if a<8 else 9):

               for d in range(b-1 if b>0 else 0, b+2 if b<8 else 9):

                   if matriz[c][d][0] == '●': cont_minas_adj += 1

           matriz[a][b][0] = str(cont_minas_adj)

   return matriz



def colocar_bandeira(x, y, matriz):

   try:

       matriz[x][y][1] = '▲' if matriz[x][y][1] != '▲' else '_'

   except IndexError:

       pass

   return matriz



def posicoes_restantes(matriz):

   rest = 0

   for l in matriz:

       for j in l:

           if len(j) == 2:

               rest += 1

   return rest



def mostrar_resultados(vit, der):

   print("=== Seus Resultados ===")

   print(f"Vitórias: {vit}")

   print(f"Derrotas: {der}")

   print("=" * 23)



def ler_posicao(pos):

   while True:

       try:

           var = int(input(f"Informe a {pos} que deseja selecionar [1-9]: ")) - 1

           if var >= 0 and var < 9:

               return var

       except ValueError:

           pass

       print("Opção inválida!")



def ler_opcao():

   while True:

       try:

           opt = str(input("Informe o que deseja realizar\n[a] Abrir [b] Bandeira: "))[0].lower()

           if opt== 'a': return 1

           elif opt == 'b': return 2

       except IndexError:

           pass

       print("Opção inválida!")



# Incialização do jogo

vitorias = 0

derrotas = 0

RUNNING = True


while RUNNING: # laço principal do jogo

   # Nessa lógica cada posição da matriz tem o back matriz[a][b][0] onde ficam os números e as bombas #

   # e o front matrz[a][b][1] onde ficam os valores que serão mostrados, caso não haja front, será #

   # mostrado o valor do back (quando a área já foi descoberta) #

   matriz = [[['0', '_'] for c in range(0, 9)] for d in range(0, 9)]

   matriz = inserir_bombas(matriz)

   matriz = calcular_minas_adjacentes(matriz)


   while RUNNING:

       mostrar_matriz(matriz)


       # Recebe e processa os dados

       x, y = ler_posicao("linha"), ler_posicao("coluna")

       if ler_opcao() == 1:

           if matriz[x][y][0] == '●':

               derrotas += 1

               mostrar_matriz_debug(matriz)

               print("Você perdeu!")

               if str(input("Quer jogar novamente [s/n]: ")).lower() != 's':

                   mostrar_resultados(vitorias, derrotas)

                   RUNNING = False

               break

           elif matriz[x][y][0] == '0':

               # exibir área complexa

               if len(matriz[x][y]) == 2: matriz[x][y].pop(1)

               matriz = mostrar_area_complexa(matriz)

           else:

               # exibir área simples (1 espaço)

               if len(matriz[x][y]) == 2: matriz[x][y].pop(1)


           # Verifica se o jogador venceu

           if posicoes_restantes(matriz) <= 13:

               vitorias += 1

               mostrar_matriz_debug(matriz)

               print("Você VENCEU!!!")

               if str(input("Quer jogar novamente [s/n]: ")).lower() != 's':

                   mostrar_resultados(vitorias, derrotas)

                   RUNNING = False

               break

       else:

           matriz = colocar_bandeira(x, y, matriz)
