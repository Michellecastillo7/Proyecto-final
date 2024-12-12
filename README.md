# Autor: Michel Castillo
# Catedrático: Arnol Rafael Gutiérrez
from abc import ABC, abstractmethod

# Clase base abstracta para usuarios
class Usuario(ABC):
    def __init__(self, id, nombre, email):
        self.id = id  # Identificador único del usuario
        self.nombre = nombre  # Nombre del usuario
        self.email = email  # Email del usuario

    @abstractmethod
    def get_info(self):
        pass

    def __str__(self):
        # Representación en texto del usuario
        return f"{self.id}: {self.nombre} ({self.email})"

class Estudiante(Usuario):
    def __init__(self, id, nombre, email):
        super().__init__(id, nombre, email)
        self.cursos_inscritos = []  # Lista de cursos en los que está inscrito
        self.calificaciones = {}  # Diccionario de calificaciones por curso

    def inscribir_curso(self, curso):
        # Inscribe al estudiante en un curso, si no lo está ya
        if curso not in self.cursos_inscritos:
            self.cursos_inscritos.append(curso)
            curso.agregar_estudiante(self)
        else:
            print("El estudiante ya está inscrito en este curso.")

    def agregar_calificacion(self, curso, calificacion):
        # Añade una calificación al estudiante para un curso
        if curso in self.cursos_inscritos:
            self.calificaciones[curso] = calificacion
        else:
            print("El estudiante no está inscrito en este curso.")

    def calcular_promedio(self):
        # Calcula el promedio de las calificaciones
        if not self.calificaciones:
            return 0
        return sum(self.calificaciones.values()) / len(self.calificaciones)

    def get_info(self):
        # Información resumida del estudiante
        return f"Estudiante: {self.nombre}, Cursos: {len(self.cursos_inscritos)}"

class Profesor(Usuario):
    def __init__(self, id, nombre, email):
        super().__init__(id, nombre, email)
        self.cursos_asignados = []  # Lista de cursos asignados al profesor

    def asignar_curso(self, curso):
        # Asigna un curso al profesor, si no lo está ya
        if curso not in self.cursos_asignados:
            self.cursos_asignados.append(curso)
            curso.asignar_profesor(self)
        else:
            print("El profesor ya está asignado a este curso.")

    def get_info(self):
        # Información resumida del profesor
        return f"Profesor: {self.nombre}, Cursos: {len(self.cursos_asignados)}"

class Curso:
    def __init__(self, id, nombre):
        self.id = id  # Identificador único del curso
        self.nombre = nombre  # Nombre del curso
        self.profesor = None  # Profesor asignado al curso
        self.estudiantes = []  # Lista de estudiantes inscritos

    def agregar_estudiante(self, estudiante):
        # Añade un estudiante al curso, si no lo está ya
        if estudiante not in self.estudiantes:
            self.estudiantes.append(estudiante)
        else:
            print("El estudiante ya está en el curso.")

    def asignar_profesor(self, profesor):
        # Asigna un profesor al curso
        self.profesor = profesor

    def listar_estudiantes(self):
        # Lista de estudiantes inscritos en el curso
        return [str(estudiante) for estudiante in self.estudiantes]

    def __str__(self):
        # Representación en texto del curso
        return f"Curso: {self.nombre}, Profesor: {self.profesor.nombre if self.profesor else 'No asignado'}"

class SistemaGestion:
    def __init__(self):
        self.usuarios = {}  # Diccionario de usuarios registrados
        self.cursos = {}  # Diccionario de cursos creados

    def registrar_usuario(self, tipo, id, nombre, email):
        # Registra un usuario de tipo estudiante o profesor
        if id in self.usuarios:
            print("Ya existe un usuario con este ID.")
            return

        if tipo == "estudiante":
            self.usuarios[id] = Estudiante(id, nombre, email)
        elif tipo == "profesor":
            self.usuarios[id] = Profesor(id, nombre, email)
        else:
            print("Tipo de usuario inválido.")

    def crear_curso(self, id, nombre):
        # Crea un nuevo curso si no existe uno con el mismo ID
        if id in self.cursos:
            print("Ya existe un curso con este ID.")
            return

        self.cursos[id] = Curso(id, nombre)

    def asignar_profesor_a_curso(self, id_curso, id_profesor):
        # Asigna un profesor a un curso
        curso = self.cursos.get(id_curso)
        profesor = self.usuarios.get(id_profesor)

        if not curso:
            print("El curso no existe.")
            return
        if not isinstance(profesor, Profesor):
            print("El ID ingresado no pertenece a un profesor.")
            return

        profesor.asignar_curso(curso)

    def inscribir_estudiante_a_curso(self, id_curso, id_estudiante):
        # Inscribe un estudiante a un curso
        curso = self.cursos.get(id_curso)
        estudiante = self.usuarios.get(id_estudiante)

        if not curso:
            print("El curso no existe.")
            return
        if not isinstance(estudiante, Estudiante):
            print("El ID ingresado no pertenece a un estudiante.")
            return

        estudiante.inscribir_curso(curso)

    def generar_reporte_estudiante(self, id_estudiante):
        # Genera un reporte de calificaciones para un estudiante
        estudiante = self.usuarios.get(id_estudiante)

        if not isinstance(estudiante, Estudiante):
            print("El ID ingresado no pertenece a un estudiante.")
            return

        print(f"Reporte de {estudiante.nombre}:")
        for curso, calificacion in estudiante.calificaciones.items():
            print(f"- Curso: {curso.nombre}, Calificación: {calificacion}")
        print(f"Promedio General: {estudiante.calcular_promedio():.2f}")

    def listar_cursos(self):
        # Lista todos los cursos creados
        for curso in self.cursos.values():
            print(curso)

# Menú interactivo basado en simulación de entradas
def menu():
    sistema = SistemaGestion()

    acciones = [
        ("1", lambda: sistema.registrar_usuario("estudiante", "E001", "Michel Castillo", "michel@example.com")),
        ("2", lambda: sistema.registrar_usuario("profesor", "P001", "Arnol Gutiérrez", "arnol@example.com")),
        ("3", lambda: sistema.crear_curso("C001", "Matemáticas")),
        ("4", lambda: sistema.asignar_profesor_a_curso("C001", "P001")),
        ("5", lambda: sistema.inscribir_estudiante_a_curso("C001", "E001")),
        ("6", lambda: sistema.generar_reporte_estudiante("E001")),
        ("7", lambda: sistema.listar_cursos()),
    ]

    # Ejecución de cada acción en secuencia
    for accion in acciones:
        print(f"Ejecutando opción {accion[0]}...")
        accion[1]()

if __name__ == "__main__":
    menu()
