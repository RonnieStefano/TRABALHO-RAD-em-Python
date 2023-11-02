# TRABALHO-RAD-em-Python
import sqlite3
import tkinter as tk
from tkinter import messagebox, simpledialog

def criar_tabela_treinadores():
    conn = sqlite3.connect('academia.db')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS treinadores (
            id INTEGER PRIMARY KEY,
            nome TEXT,
            especialidade TEXT,
            salario REAL,
            telefone TEXT
        )
    ''')
    conn.commit()
    conn.close()

def adicionar_treinador():
    nome = simpledialog.askstring("Adicionar Treinador", "Nome:")
    especialidade = simpledialog.askstring("Adicionar Treinador", "Especialidade:")
    salario = simpledialog.askfloat("Adicionar Treinador", "Salário:")
    telefone = simpledialog.askstring("Adicionar Treinador", "Telefone:")

    conn = sqlite3.connect('academia.db')
    cursor = conn.cursor()
    cursor.execute('INSERT INTO treinadores (nome, especialidade, salario, telefone) VALUES (?, ?, ?, ?)', (nome, especialidade, salario, telefone))
    conn.commit()
    conn.close()

    messagebox.showinfo("Sucesso", "Treinador adicionado com sucesso!")

def listar_treinadores():
    conn = sqlite3.connect('academia.db')
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM treinadores')
    treinadores = cursor.fetchall()
    conn.close()

    if treinadores:
        treinadores_str = "Lista de Treinadores:\n\n"
        for treinador in treinadores:
            treinadores_str += f"ID: {treinador[0]}\nNome: {treinador[1]}\nEspecialidade: {treinador[2]}\nSalário: {treinador[3]}\nTelefone: {treinador[4]}\n\n"
        messagebox.showinfo("Treinadores Cadastrados", treinadores_str)
    else:
        messagebox.showinfo("Nenhum treinador", "Nenhum treinador cadastrado.")

def excluir_treinador():
    treinador_id = simpledialog.askinteger("Excluir Treinador", "ID do Treinador:")

    conn = sqlite3.connect('academia.db')
    cursor = conn.cursor()
    cursor.execute('DELETE FROM treinadores WHERE id=?', (treinador_id,))
    conn.commit()
    conn.close()

    messagebox.showinfo("Sucesso", "Treinador excluído!")

def atualizar_treinador():
    treinador_id = simpledialog.askinteger("Atualizar Treinador", "ID do Treinador:")
    conn = sqlite3.connect('academia.db')
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM treinadores WHERE id=?', (treinador_id,))
    treinador = cursor.fetchone()
    conn.close()

    if treinador:
        novo_nome = simpledialog.askstring("Atualizar Treinador", "Novo Nome:", initialvalue=treinador[1])
        nova_especialidade = simpledialog.askstring("Atualizar Treinador", "Nova Especialidade:", initialvalue=treinador[2])
        novo_salario = simpledialog.askfloat("Atualizar Treinador", "Novo Salário:", initialvalue=treinador[3])
        novo_telefone = simpledialog.askstring("Atualizar Treinador", "Novo Telefone:", initialvalue=treinador[4])

        if novo_nome is not None:
            conn = sqlite3.connect('academia.db')
            cursor = conn.cursor()
            cursor.execute('''
                UPDATE treinadores
                SET nome=?, especialidade=?, salario=?, telefone=?
                WHERE id=?
            ''', (novo_nome, nova_especialidade, novo_salario, novo_telefone, treinador_id))
            conn.commit()
            conn.close()
            messagebox.showinfo("Sucesso", "Detalhes do treinador atualizados!")
    else:
        messagebox.showinfo("Erro", "Treinador não encontrado.")

def main():
    criar_tabela_treinadores()

    root = tk.Tk()
    root.title("Sistema de Gestão de Treinadores")

    font = ("Arial", 12)
    bg_color = "#f0f0f0"
    label_color = "#333"

    root.configure(bg=bg_color)

    adicionar_treinador_button = tk.Button(root, text="Adicionar Treinador", command=adicionar_treinador, font=font)
    adicionar_treinador_button.pack()

    listar_treinadores_button = tk.Button(root, text="Listar Treinadores", command=listar_treinadores, font=font)
    listar_treinadores_button.pack()

    excluir_treinador_button = tk.Button(root, text="Excluir Treinador", command=excluir_treinador, font=font)
    excluir_treinador_button.pack()

    atualizar_treinador_button = tk.Button(root, text="Atualizar Treinador", command=atualizar_treinador, font=font)
    atualizar_treinador_button.pack()

    root.mainloop()

main()
