from abc import ABC, abstractmethod
from datetime import date
from datetime import datetime

class Historico:
    def __init__(self):
        self.transacoes = []

    def adicionar_transacao(self, transacao):
        self.transacoes.append(transacao)


class Transacao(ABC):
    @abstractmethod
    def registrar(self, conta):
        pass


class Saque(Transacao):
    def __init__(self, valor):
        self.valor = valor

    def registrar(self, conta):
        sucesso = conta.sacar(self.valor)
        if sucesso:
            conta.historico.adicionar_transacao(self)



class Deposito(Transacao):
    def __init__(self, valor):
        self.valor = valor

    def registrar(self, conta):
        sucesso = conta.depositar(self.valor)
        if sucesso:
            conta.historico.adicionar_transacao(self)



class Cliente(ABC):
    def __init__(self, endereco):
        self.endereco = endereco
        self.contas = []

    def adicionar_conta(self, conta):
        self.contas.append(conta)

    def realizar_transacao(self, conta, transacao):
        transacao.registrar(conta)


class PessoaFisica(Cliente):
    def __init__(self, nome, cpf, data_nascimento, endereco):
        super().__init__(endereco)
        self.nome = nome
        self.cpf = cpf
        self.data_nascimento = data_nascimento


class Conta:
    def __init__(self, numero, cliente, agencia="0001"):
        self.saldo = 0.0
        self.numero = numero
        self.agencia = agencia
        self.cliente = cliente
        self.historico = Historico()

    def saldo(self):
        return self.saldo

    def sacar(self, valor):
        if valor <= 0:
            print("Operação falhou! O valor informado é inválido.")
            return False

        if valor > self.saldo:
            print("Operação falhou! Você não tem saldo suficiente.")
            return False

        if valor > self.limite:
            print("Operação falhou! O valor do saque excede o limite.")
            return False

        if self.numero_saques >= self.limite_saques:
            print("Operação falhou! Número máximo de saques excedido.")
            return False

        self.saldo -= valor
        self.numero_saques += 1
        print(f"Saque de R$ {valor:.2f} realizado com sucesso.")
        return True


    def depositar(self, valor):
        if valor <= 0:
            print("Operação falhou! O valor informado é inválido.")
            return False

        self.saldo += valor
        print(f"Depósito de R$ {valor:.2f} realizado com sucesso.")
        return True


    @classmethod
    def nova_conta(cls, cliente, numero):
        return cls(numero, cliente)


class ContaCorrente(Conta):
    def __init__(self, numero, cliente, limite=500.0, limite_saques=3):
        super().__init__(numero, cliente)
        self.limite = limite
        self.limite_saques = limite_saques
        self.numero_saques = 0


menu = """
[d] Depositar
[s] Sacar
[e] Extrato
[u] Cadastro de Usuário
[c] Cadastro de Conta
[l] Listar Contas
[q] Sair
=> """

clientes = []
contas = []
numero_conta = 1  # vai sendo incrementado a cada nova conta

while True:
    opcao = input(menu)

    if opcao == "u":
        cpf = input("Informe o CPF (somente números): ").strip()
        if any(c.cpf == cpf for c in clientes):
            print("CPF já cadastrado.")
            continue

        nome = input("Informe o nome completo: ").strip()
        nascimento = input("Data de nascimento (dd/mm/aaaa): ").strip()
        endereco = input("Endereço (logradouro, nro - bairro - cidade/UF): ").strip()

        data_nascimento = datetime.strptime(nascimento, "%d/%m/%Y").date()
        novo_cliente = PessoaFisica(nome, cpf, data_nascimento, endereco)
        clientes.append(novo_cliente)
        print("Usuário cadastrado com sucesso.")

    elif opcao == "c":
        cpf = input("Informe o CPF do cliente: ").strip()
        cliente = next((c for c in clientes if c.cpf == cpf), None)

        if not cliente:
            print("Cliente não encontrado. Cadastre primeiro.")
            continue

        conta = ContaCorrente(numero=numero_conta, cliente=cliente)
        cliente.adicionar_conta(conta)
        contas.append(conta)
        numero_conta += 1

        print("Conta criada com sucesso!")

    elif opcao == "d":
        cpf = input("Informe o CPF do titular: ").strip()
        cliente = next((c for c in clientes if c.cpf == cpf), None)

        if not cliente or not cliente.contas:
            print("Cliente ou conta não encontrada.")
            continue

        valor = float(input("Informe o valor do depósito: "))
        deposito = Deposito(valor)
        cliente.realizar_transacao(cliente.contas[0], deposito)

    elif opcao == "s":
        cpf = input("Informe o CPF do titular: ").strip()
        cliente = next((c for c in clientes if c.cpf == cpf), None)

        if not cliente or not cliente.contas:
            print("Cliente ou conta não encontrada.")
            continue

        valor = float(input("Informe o valor do saque: "))
        saque = Saque(valor)
        cliente.realizar_transacao(cliente.contas[0], saque)

    elif opcao == "e":
        cpf = input("Informe o CPF do titular: ").strip()
        cliente = next((c for c in clientes if c.cpf == cpf), None)

        if not cliente or not cliente.contas:
            print("Cliente ou conta não encontrada.")
            continue

        conta = cliente.contas[0]
        print("\n========= EXTRATO =========")
        if not conta.historico.transacoes:
            print("Não foram realizadas movimentações.")
        else:
            for transacao in conta.historico.transacoes:
                tipo = transacao.__class__.__name__
                print(f"{tipo}: R$ {transacao.valor:.2f}")
        print(f"\nSaldo atual: R$ {conta.saldo:.2f}")
        print("==============================")

    elif opcao == "l":
        if not contas:
            print("Nenhuma conta cadastrada.")
            continue
        for conta in contas:
            print(f"Agência: {conta.agencia}")
            print(f"Conta: {conta.numero}")
            print(f"Titular: {conta.cliente.nome}")
            print("-----------------------------")

    elif opcao == "q":
        break

    else:
        print("Operação inválida. Tente novamente.")
