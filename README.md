```python
from tkinter import *
from tkinter import messagebox
import sqlite3

# -------funciones-----------
def conexionBBDD():
    miConexion = sqlite3.connect("Usuarios")
    miCursor = miConexion.cursor()
    try:
        miCursor.execute('''
            CREATE TABLE DATOSUSUARIOS (
            ID INTEGER PRIMARY KEY AUTOINCREMENT,
            NOMBRE_USUARIO VARCHAR(50),
            PASSWORD VARCHAR(50),
            APELLIDO VARCHAR(10),
            DIRECCION VARCHAR(50),
            COMENTARIOS VARCHAR(100))
        ''')
        messagebox.showinfo("BBDD", "BBDD creada con éxito")
    except:
        messagebox.showwarning("Atención", "La BBDD ya existe")

def salirAplicacion():
    valor = messagebox.askquestion("Salir", "¿Deseas salir de la aplicación?")
    if valor == "yes":
        root.destroy()

def limpiarCampos():
    miNombre.set("")
    miID.set("")
    miDireccion.set("")
    miApellido.set("")
    miPass.set("")
    textoComentario.delete(1.0, END)

def crear():
    miConexion = sqlite3.connect("Usuarios")
    miCursor = miConexion.cursor()
    miCursor.execute("INSERT INTO DATOSUSUARIOS VALUES(NULL, '" + miNombre.get() + "','" + miPass.get() + "','" + miApellido.get() + "','" + miDireccion.get() + "','" + textoComentario.get(1.0, END) + "')")
    miConexion.commit()
    messagebox.showinfo("BBDD", "Registro insertado con éxito")

def leer():
    miConexion = sqlite3.connect("Usuarios")
    miCursor = miConexion.cursor()
    miCursor.execute("SELECT * FROM DATOSUSUARIOS WHERE ID=" + miID.get())
    elUsuario = miCursor.fetchall()
    for usuario in elUsuario:
        miID.set(usuario[0])
        miNombre.set(usuario[1])
        miPass.set(usuario[2])
        miApellido.set(usuario[3])
        miDireccion.set(usuario[4])
        textoComentario.insert(1.0, usuario[5])
    miConexion.commit()

def actualizar():
    miConexion = sqlite3.connect("Usuarios")
    miCursor = miConexion.cursor()
    miCursor.execute("UPDATE DATOSUSUARIOS SET NOMBRE_USUARIO='" + miNombre.get() + "', PASSWORD='" + miPass.get() + "', APELLIDO='" + miApellido.get() + "', DIRECCION='" + miDireccion.get() + "', COMENTARIOS='" + textoComentario.get("1.0", END) + "' WHERE ID=" + miID.get())
    miConexion.commit()
    messagebox.showinfo("BBDD", "Registro actualizado con éxito")

def eliminar():
    miConexion = sqlite3.connect("Usuarios")
    miCursor = miConexion.cursor()
    miCursor.execute("DELETE FROM DATOSUSUARIOS WHERE ID=" + miID.get())
    miConexion.commit()
    messagebox.showinfo("BBDD", "Registro borrado con éxito")

root = Tk()
barramenu = Menu(root)
root.config(menu=barramenu, width=1000, height=300)

bbddMenu = Menu(barramenu, tearoff=0)
bbddMenu.add_command(label="Conectar", command=conexionBBDD)
bbddMenu.add_command(label="Salir", command=salirAplicacion)

borrarMenu = Menu(barramenu, tearoff=0)
borrarMenu.add_command(label="Borrar campos", command=limpiarCampos)

CrudMenu = Menu(barramenu, tearoff=0)
CrudMenu.add_command(label="Crear", command=crear)
CrudMenu.add_command(label="Leer", command=leer)
CrudMenu.add_command(label="Actualizar", command=actualizar)
CrudMenu.add_command(label="Borrar", command=eliminar)

ayudaMenu = Menu(barramenu, tearoff=0)
ayudaMenu.add_command(label="Licencia")
ayudaMenu.add_command(label="Acerca de...")

barramenu.add_cascade(label="BBDD", menu=bbddMenu)
barramenu.add_cascade(label="Borrar", menu=borrarMenu)
barramenu.add_cascade(label="CRUD", menu=CrudMenu)
barramenu.add_cascade(label="Ayuda", menu=ayudaMenu)

root.title("Programa Base de Datos")

# ---------------------campos------------------------------
miFrame = Frame(root)
miFrame.pack()

miID = StringVar()
miNombre = StringVar()
miApellido = StringVar()
miPass = StringVar()
miDireccion = StringVar()

CuadroID = Entry(miFrame, textvariable=miID)
CuadroID.grid(row=0, column=1, padx=10, pady=10)

CuadroNombre = Entry(miFrame, textvariable=miNombre)
CuadroNombre.grid(row=1, column=1, padx=10, pady=10)
CuadroNombre.config(fg="red", justify="right")

CuadroPass = Entry(miFrame, textvariable=miPass)
CuadroPass.grid(row=2, column=1, padx=10, pady=10)
CuadroPass.config(show="?")

cuadroApellido = Entry(miFrame, textvariable=miApellido)
cuadroApellido.grid(row=3, column=1, padx=10, pady=10)

cuadroDireccion = Entry(miFrame, textvariable=miDireccion)
cuadroDireccion.grid(row=4, column=1, padx=10, pady=10)

textoComentario = Text(miFrame, width=30, height=20)
textoComentario.grid(row=5, column=1, padx=10, pady=10)

scrollVert = Scrollbar(miFrame, command=textoComentario.yview)
scrollVert.grid(row=5, column=2, sticky="nsew")

textoComentario.config(yscrollcommand=scrollVert.set)

# ---------------------labels------------------------------
idLabel = Label(miFrame, text="Id:")
idLabel.grid(row=0, column=0, sticky="e", padx=10, pady=10)

nombreLabel = Label(miFrame, text="Nombre:")
nombreLabel.grid(row=1, column=0, sticky="e", padx=10, pady=10)

passLabel = Label(miFrame, text="Password:")
passLabel.grid(row=2, column=0, sticky="e", padx=10, pady=10)

apellidoLabel = Label(miFrame, text="Apellido:")
apellidoLabel.grid(row=3, column=0, sticky="e", padx=10, pady=10)

direccionLabel = Label(miFrame, text="Dirección:")
direccionLabel.grid(row=4, column=0, sticky="e", padx=10, pady=10)

comentariosLabel = Label(miFrame, text="Comentarios:")
comentariosLabel.grid(row=5, column=0, sticky="e", padx=10, pady=10)

# ---------------------botones------------------------------
miFrame2 = Frame(root)
miFrame2.pack()

botonCrear = Button(miFrame2, text="Create", command=crear)
botonCrear.grid(row=1, column=0, sticky="e", padx=10, pady=10)

botonLeer = Button(miFrame2, text="Leer", command=leer)
botonLeer.grid(row=1, column=1, sticky="e", padx=10, pady=10)

botonActualizar = Button(miFrame2, text="Update", command=actualizar)
botonActualizar.grid(row=1, column=2, sticky="e", padx=10, pady=10)

botonBorrar = Button(miFrame2, text="Borrar", command=eliminar)
botonBorrar.grid(row=1, column=3, sticky="e", padx=10, pady=10)

root.mainloop()
```
