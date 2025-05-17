# Criando-um-Sistema-Banc-rio


menu = """
[d] Depositar
[s] Sacar
[e] Extrato
[q] Sair
=> """

saldo = 0
limite = 500
extrato = ""
numero_saques = 0
LIMITE_SAQUES = 3

while True:
    opcao = input(menu)

    if opcao == "d":
      valor = float(input("Informe o valor: "))

      if valor < 0:
        print('Operação inválida!')
      else:
        saldo += valor
        extrato += "Depósito: R$ {:.2f}\n".format(valor)

    elif opcao == "s":
      valor = float(input("Informe o valor: "))

      if valor > saldo:
        print('Operação inválida! Saldo insuficiente!')
      elif valor > limite:
        print('Operação inválida! Valor superior ao limite!')
      elif numero_saques >= LIMITE_SAQUES:
        print('Operação inválida! Limite de saques excedido!')
      else:
        if valor < 0:
          print('Operação inválida!')
        else:
          numero_saques += 1
          saldo -= valor
          extrato += "Saque: R$ {:.2f}\n".format(valor)

    elif opcao == "e":
      if not extrato:
        print("Não foram realizadas movimentações.")
      else:
        print("---------EXTRATO---------")
        print(extrato)
        print("\nSaldo: R$ {:.2f}\n".format(saldo))

    elif opcao == "q":
      break
    
    else:
        print("Operação inválida, por favor selecione novamente a operação desejada.")
