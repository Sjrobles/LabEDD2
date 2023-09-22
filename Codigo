import IPython.display
from IPython.display import display, Image
import csv
import graphviz
import pandas as pd
import folium
from folium.plugins import Draw

from collections import deque
from IPython.display import display
from typing import Any, Optional, Tuple
from google.colab.patches import cv2_imshow
import matplotlib.pyplot as plt
!pip install graphviz
!pip install pydotplus
!dot -V


# leer el csv desde el link
url = 'https://drive.google.com/uc?id=1iY0mSe-5UiDx0Tu0lZsurVgxMDgT5ByZ'
db = pd.read_csv(url)

metrica1 = []
metrica2 = []

lista_categorias = ["titulo", "departamento", "ciudad", "tipo de propiedad", "latitud",
                    "longitud", "superficie total", "superficie cubierta", "habitaciones",
                    "baños", "tipo de operación,", "price", "ninguno"]
lista_surfacecover = db['surface_covered'].tolist()
lista_bedrooms = db['bedrooms'].tolist()
lista_surface = db['surface_total'].tolist()
lista_price = db['price'].tolist()
lista_title = db['title'].tolist()
lista_department = db['department'].tolist()
lista_city = db['city'].tolist()
lista_property_type = db['property_type'].tolist()
lista_latitude = db['latitude'].tolist()
lista_longitude = db['longitude'].tolist()
lista_bathrooms = db['bathrooms'].tolist()
lista_operation_type = db['operation_type'].tolist()

for i in range(len(lista_price)):
  resultado = lista_price[i] // lista_surface[i]
  resultado2 = (lista_price[i] // lista_surfacecover[i]) * lista_bedrooms[i]
  metrica1.append(resultado)
  metrica2.append(resultado2)
db

class Node:
    def __init__(self,title:str, department: str, city:str, property_type:str,
                 latitude:bool, longitude:bool, surface_total:bool, surface_covered:bool, bedrooms:int,
                 bathrooms:int, operation_type:str, price: int):

        self.title = title
        self.department = department
        self.city = city
        self.property_type = property_type
        self.latitude = latitude
        self.longitude = longitude
        self.surface_total = surface_total
        self.surface_covered = surface_covered
        self.bedrooms = bedrooms
        self.bathrooms = bathrooms
        self.operation_type = operation_type
        self.price = price
        self.valor = self.price // self.surface_total
        self.valor2 = (self.price // self.surface_total) * (self.bedrooms + self.latitude + self.longitude)
        self.right = None
        self.left = None
        self.height = 1
    def __str__(self):
        return f"Node(title='{self.title}', department='{self.department}', city='{self.city}', property_type='{self.property_type}', latitude={self.latitude}, longitude={self.longitude}, surface_total={self.surface_total}, surface_covered={self.surface_covered}, bedrooms={self.bedrooms}, bathrooms={self.bathrooms}, operation_type='{self.operation_type}', price={self.price})"



def get_height(node):
    if node is None:
        return 0
    return node.height

def update_height(node):
    node.height = 1 + max(get_height(node.left), get_height(node.right))

def balance_factor(node):
    if node is None:
        return 0
    return get_height(node.left) - get_height(node.right)

def rotate_left(node):
    right_child = node.right
    node.right = right_child.left
    right_child.left = node
    update_height(node)
    update_height(right_child)
    return right_child

def rotate_right(node):
    left_child = node.left
    node.left = left_child.right
    left_child.right = node
    update_height(node)
    update_height(left_child)
    return left_child

def balance(node):
    if node is None:
        return node

    update_height(node)

    if balance_factor(node) > 1:
        if balance_factor(node.left) < 0:
            node.left = rotate_left(node.left)
        return rotate_right(node)

    if balance_factor(node) < -1:
        if balance_factor(node.right) > 0:
            node.right = rotate_right(node.right)
        return rotate_left(node)

    return node

def insert(root, title, department, city, property_type, latitude, longitude, surface_total, surface_covered,
           bedrooms, bathrooms, operation_type, price, valor, valor2):
    if root is None:
        return Node(title, department, city, property_type, latitude, longitude, surface_total, surface_covered,
                    bedrooms, bathrooms, operation_type, price)

    if valor < root.valor:
        root.left = insert(root.left, title, department, city, property_type, latitude, longitude, surface_total,
                           surface_covered, bedrooms, bathrooms, operation_type, price, valor, valor2)
    elif valor > root.valor:
        root.right = insert(root.right, title, department, city, property_type, latitude, longitude, surface_total,
                            surface_covered, bedrooms, bathrooms, operation_type, price, valor, valor2)
    else:
        # Los valores son iguales, compara por valor2
        if valor2 < root.valor2:
            root.left = insert(root.left, title, department, city, property_type, latitude, longitude, surface_total,
                               surface_covered, bedrooms, bathrooms, operation_type, price, valor, valor2)
        else:
            root.right = insert(root.right, title, department, city, property_type, latitude, longitude, surface_total,
                                surface_covered, bedrooms, bathrooms, operation_type, price, valor, valor2)

    return balance(root)

def insert_nodo(root,node):
    if root is None:
        return node

    if node.valor < root.valor:
      root.left = insert_nodo(root.left,node)

    elif node.valor > root.valor:
      root.right = insert_nodo(root.right,node)

    else:
        # Los valores son iguales, compara por valor2
        if node.valor2 < root.valor2:
          root.left = insert_nodo(root.left,node)

        else:
          root.right = insert_nodo(root.right,node)


    return balance(root)

# Función para eliminar un nodo en el árbol AVL
def delete(root, metrica1, metrica2):
    if root is None:
        return root

    # Realiza una búsqueda para encontrar el nodo que se va a eliminar
    if metrica1 < root.valor or (metrica1 == root.valor and metrica2 < root.valor2):
        root.left = delete(root.left, metrica1, metrica2)

    elif metrica1 > root.valor or (metrica1 == root.valor and metrica2 > root.valor2):
        root.right = delete(root.right, metrica1, metrica2)
    else:
        # Este es el nodo que queremos eliminar

        # Caso 1: Nodo con un solo hijo o sin hijos
        if root.left is None:
            temp = root.right
            root = None
            return temp
        elif root.right is None:
            temp = root.left
            root = None
            return temp

        # Caso 2: Nodo con dos hijos, obtenemos el sucesor inmediato en el subárbol derecho
        temp = find_min(root.right)

        # Copia los datos del sucesor inmediato al nodo actual
        root.title, root.department, root.city, root.property_type, root.latitude, root.longitude, \
        root.surface_total, root.surface_covered, root.bedrooms, root.bathrooms, root.operation_type, \
        root.precio, root.valor, root.valor2 = temp.title, temp.department, temp.city, temp.property_type, \
                                               temp.latitude, temp.longitude, temp.surface_total, temp.surface_covered, \
                                               temp.bedrooms, temp.bathrooms, temp.operation_type, temp.precio, temp.valor, temp.valor2

        # Elimina el sucesor inmediato
        root.right = delete(root.right, temp.valor, temp.valor2)

    # Realiza el reequilibrio después de la eliminación
    return balance(root)

# Función para encontrar el valor mínimo en el árbol
def find_min(node):
    current = node
    while current.left is not None:
        current = current.left
    return current

# Función para buscar nodos en un árbol AVL
def buscar(nodo, clave):
    if nodo is None:
        return nodos
    nodos = []
    if nodo.clave == clave:
        nodos.append(nodo)
        buscar(nodo.izq, clave, nodos)
        buscar(nodo.der, clave, nodos)
    elif nodo.clave > clave:
        buscar(nodo.izq, clave, nodos)
    else:
        buscar(nodo.der, clave, nodos)

def search(node, value):
    # Si el nodo está vacío, no hay nada que buscar
    if node is None:
        return None

    # Si los valores buscados son iguales a los valores del nodo actual, lo hemos encontrado
    if value == node.valor:
        return node

    # Si los valores buscados son menores que los valores del nodo actual, buscamos en el subárbol izquierdo
    elif value < node.valor:
        return search(node.left, value)

    # Si los valores buscados son mayores que los valores del nodo actual, buscamos en el subárbol derecho
    else:
        return search(node.right, value)



def metrics(node: 'Node', metrica: 'list',operador,valor,c_m):
    # Si el nodo está vacío, no hay nada que buscar
    if node is None:
        return []

    # Compara los valores de ciudad, cuartos y superficie con los valores del nodo actual
    nodos_filtrados = []
    if c_m == 2:
      if (eval(f'node.{metrica[0]} {operador[0]} "{valor[0]}"' if isinstance(valor[0], str) else f'node.{metrica[0]} {operador[0]} {valor[0]}') and
        eval(f'node.{metrica[1]} {operador[1]} {valor[1]}' if isinstance(valor[1], int) else f'node.{metrica[1]} {operador[1]} "{valor[1]}"')):
       nodos_filtrados.append(node)
    # else:
      if (eval(f'node.{metrica[0]} {operador[0]} "{valor[0]}"' if isinstance(valor[0], str) else f'node.{metrica[0]} {operador[0]} {valor[0]}')):
        nodos_filtrados.append(node)
    nodos_filtrados += search_metrics(node.left, metrica, operador, valor, c_m)
    nodos_filtrados += search_metrics(node.right, metrica, operador, valor, c_m)


    # Busca en los subárboles izquierdo y derecho

def find_level(root, valor, valor2, level=0):
    # Si el árbol está vacío, el nodo no se encuentra
    if root is None:
        return -1
    # Si el valor y el título buscados son iguales al valor y al título del nodo actual, hemos encontrado el nodo y su nivel
    if valor == root.valor and valor2 == root.valor2:
        return level
    # Si el valor buscado es menor que el valor del nodo actual, o si son iguales pero el título buscado es menor que el título del nodo actual, buscamos en el subárbol izquierdo y aumentamos el nivel en uno
    elif valor < root.valor or (valor == root.valor and valor2 < root.valor2):
        return find_level(root.left, valor, valor2, level + 1)
    # Si el valor buscado es mayor que el valor del nodo actual, o si son iguales pero el título buscado es mayor que el título del nodo actual, buscamos en el subárbol derecho y aumentamos el nivel en uno
    else:
        return find_level(root.right, valor, valor2, level + 1)


def find_parent(root, valor, valor2, parent=None):
    # Si el árbol está vacío, no hay nodo padre
    if root is None:
        return None

    # Si encontramos el nodo cuyo padre estamos buscando, retornamos el nodo padre
    if valor == root.valor and valor2 == root.valor2:
        return parent

    # Si el valor buscado es menor que el valor del nodo actual, o si son iguales pero el valor2 buscado es menor que el valor2 del nodo actual, buscamos en el subárbol izquierdo y actualizamos el nodo padre al nodo actual
    elif valor < root.valor or (valor == root.valor and valor2 < root.valor2):
        return find_parent(root.left, valor, valor2, parent=root)

    # Si el valor buscado es mayor que el valor del nodo actual, o si son iguales pero el valor2 buscado es mayor que el valor2 del nodo actual, buscamos en el subárbol derecho y actualizamos el nodo padre al nodo actual
    else:
        return find_parent(root.right, valor, valor2, parent=root)


def find_grandparent(root, valor, valor2):
    # Encuentra el nodo padre del nodo dado
    parent = find_parent(root, valor, valor2)

    # Si el nodo padre es None (es decir, el nodo dado es la raíz o no se encuentra en el árbol),
    # no hay abuelo, así que retornamos None
    if parent is None:
        return None

    # Usamos la función find_parent nuevamente para encontrar el abuelo
    grandparent = find_parent(root, parent.valor, parent.valor2)
    return grandparent


def find_uncle(root, valor, valor2):
    # Encuentra el nodo abuelo del nodo dado
    grandparent = find_grandparent(root, valor, valor2)

    # Si el nodo abuelo es None (es decir, el nodo dado es la raíz o no se encuentra en el árbol),
    # no hay tío, así que retornamos None
    if grandparent is None:
        return "No tiene tio."

    # Encuentra el nodo padre del nodo dado
    parent = find_parent(root, valor, valor2)

    # Si el nodo padre es el hijo izquierdo del abuelo, el tío será el hijo derecho del abuelo y viceversa
    if parent == grandparent.left:
        return grandparent.right
    else:
        return grandparent.left



def level_order_traversal(root, queue=None, level=1, node_count=[0]):
    if not root:
        return

    if queue is None:
        queue = deque()
        queue.append((root, level))

    if not queue:
        return

    current_node, current_level = queue.popleft()
    node_count[0] += 1

    # Imprime el nodo, el número del nodo y el nivel del nodo actual
    print(f"Nodo {node_count[0]}, Nivel {current_level}: Nombre: {current_node.title}, Metrica #1: {current_node.valor}, Metrica #2: {current_node.valor2}")

    if current_node.left:
        queue.append((current_node.left, current_level + 1))
    if current_node.right:
        queue.append((current_node.right, current_level + 1))

    level_order_traversal(root, queue, current_level)

def filter_node(node: 'Node', metric, operator, value):
    if metric not in node.__dict__:
        return False  # La métrica no está en el nodo, no cumple la condición


    condition = None  # Inicializamos la variable

    if value.isdigit():
      condition = f'node.{metric} {operator} {float(value)}'
      # Encierro el valor entre comillas si es una cadena
    else:
      condition = f'node.{metric} {operator} "{value}"'
    return eval(condition)

def search_metrics(node: 'Node', metrics: 'list', operators, values):
    # Si el nodo está vacío, no hay nada que buscar
    if node is None:
        return []

    # Filtrar el nodo actual con todas las condiciones

    all_filters = all(filter_node(node, metrics[i], operators[i], values[i]) for i in range(len(metrics)))
    filtered_nodes = []

    if all_filters:
        filtered_nodes.append(node)

    filtered_nodes += search_metrics(node.left, metrics, operators, values)
    filtered_nodes += search_metrics(node.right, metrics, operators, values)

    return filtered_nodes

def mapa(result_node):
    # Crea un objeto de mapa centrado en las coordenadas dadas
    mapa = folium.Map(location=[result_node.latitude, result_node.longitude], zoom_start=15)
    Draw(export=True).add_to(mapa)
    folium.plugins.Fullscreen(
    position="topright",
    title="Expand me",
    title_cancel="Exit me",
    force_separate_button=True,
    ).add_to(mapa)

    icon_red = folium.Icon(color="red")

    text = f'DEPARTAMENTO:{result_node.department}   CIUDAD:{result_node.city}   SUPERFICIE:{result_node.surface_total}   TIPO:{result_node.property_type}   OPERACION:{result_node.operation_type}\n PRECIO:{result_node.price}'
    # Agrega un marcador al mapa en las coordenadas dadas
    folium.Marker(location=[result_node.latitude, result_node.longitude],
                  popup = folium.Popup(text, max_width=200),
                  tooltip="Click me!",
    icon=icon_red).add_to(mapa)

    # Devuelve el mapa
    return mapa
def blank():
  return ''

def mapa_list(filtered_nodes):
  mapa = folium.Map(location=[filtered_nodes[0].latitude, filtered_nodes[0].longitude], zoom_start=15)
  folium.plugins.Fullscreen(
    position="topright",
    title="Expand me",
    title_cancel="Exit me",
    force_separate_button=True,
    ).add_to(mapa)
  Draw(export=True).add_to(mapa)
  icon_red = folium.Icon(color="red")
  for result_node in filtered_nodes:
    text = f'DEPARTAMENTO:{result_node.department}   CIUDAD:{result_node.city}   SUPERFICIE:{result_node.surface_total}   TIPO:{result_node.property_type}   OPERACION:{result_node.operation_type}\n PRECIO:{result_node.price}'
    folium.Marker(location=[result_node.latitude, result_node.longitude],popup = folium.Popup(text, max_width=200),tooltip="Click me!").add_to(mapa)

  return mapa

def to_dot(node, dot=None):
    if dot is None:
        dot = graphviz.Digraph(comment='AVL Tree', strict=True)

    if node:

        label = f'Title: {node.title}\nDepartment: {node.department}\nCity: {node.city}\nProperty Type: {node.property_type}\nLatitude: {node.latitude}\nLongitude: {node.longitude}\nSurface Total: {node.surface_total}\nSurface Covered: {node.surface_covered}\nBedrooms: {node.bedrooms}\nBathrooms: {node.bathrooms}\nOperation Type: {node.operation_type}\nprice: {node.price}\nMetrica: {node.valor}\nMetrica2: {node.valor2}'
        dot.node(str(node.valor2), label=label)

        if node.left:
            dot.edge(str(node.valor2), str(node.left.valor2))
            to_dot(node.left, dot)

        if node.right:
            dot.edge(str(node.valor2), str(node.right.valor2))
            to_dot(node.right, dot)

    return dot


#Main
if __name__ == '__main__':
    root = None

for i in range(len(lista_title)) :
   valor = metrica1[i]
   valor2 = metrica2[i]
   root = insert(root, lista_title[i], lista_department[i], lista_city[i], lista_property_type[i], lista_latitude[i],
                  lista_longitude[i], lista_surface[i], lista_surfacecover[i], lista_bedrooms[i], lista_bathrooms[i],
                  lista_operation_type[i], lista_price[i], valor, valor2)
op = 10
a = blank()
while op != 0:

  print("\n** Menú de opciones **")
  print("0. salir")
  print("1. Visualizar árbol AVL")
  print("2. Añadir un nuevo nodo al árbol")
  print("3. Eliminar un nodo")
  print("4. Buscar un nodo a partir de la metrica")
  print("5. Buscar por metrica estipulada ")
  print("6. Mostrar arbol en orden por niveles")
  op = int(input("Ingrese una opción: "))

  if op == 1:

    print("El Árbol es:\n")
    dot = to_dot(root)
    image = Image(data=dot.pipe(format='png'))
    display(image)



  elif op == 2:

    print("a continuación escriba los atributos del nodo:")

    title = input("Digite el nombre de la propiedad: ")
    department = input("Digite el departamento en el que se encuentra la propiedad: ")
    city = input("Digite la ciudad en el que se encuentra el predio: ")
    property_type = input("Digite el tipo de propiedad: ")
    latitude = float(input("Digite la latitud en la que se encuentra la propiedad: "))
    longitude = float(input("Digite la longitud en la que se encuentra la propiedad: "))
    surface_total = int(input("Digite el valor de la superficie total que ocupa la propiedad: "))
    surface_covered = int(input("Digite el valor de la superficie cubierta que ocupa el predio: "))
    bedrooms = int(input("Digite la cantidad de habitaciones que tiene la propiedad: "))
    bathrooms = int(input("Digite la cantidad de baños que tiene la propiedad: "))
    operation_type = input("Digite si quiere vende o arrendar la propiedad: ")
    price = int(input("Digite el price de la propiedad: "))
    valor = price // surface_total
    valor2 = (price // surface_covered) * bedrooms + latitude + longitude

    root = insert(root, title, department, city, property_type, latitude, longitude, surface_total, surface_covered,
                  bedrooms, bathrooms, operation_type, price, valor, valor2)

  elif op == 3:

    print("A continuación escriba el valor de la metrica 1 y 2 del nodo que desea eliminar:")

    metricaa1 = float(input("Ingrese el valor de la metrica1 a eliminar: "))
    metricaa2 = float(input("Ingrese el valor de la metrica2 a eliminar: "))


    root = delete(root, metricaa1,metricaa2)






  elif op == 4:
   print("\nHa seleccionado la opción 4")
   metrica1 = float(input("Digite la métrica 1 del nodo a buscar: "))
   result_node = search(root, metrica1)

   root2 = None
   rootP = None
   rootA = None
   rootT = None

   if result_node is not None:
    print("Nodo encontrado!")
    print("Título:", result_node.title)
    print("Departamento:", result_node.department)
    print("Ciudad:", result_node.city)


    op2 = 0
    print('\nQue desea hacer con el nodo:')
    print("0. Visualizar el nodo.")
    print('1. Obtener el nivel del nodo.')
    print('2. Obtener el factor de balanceo del nodo.')
    print('3. Encontrar el padre del nodo.')
    print('4. Encontrar el abuelo del nodo.')
    print('5. Encontrar el tío del nodo.')
    op2 = int(input('Ingrese una opcion'))


    root2 = insert(root2,result_node.title,result_node.department,result_node.city,result_node.property_type,result_node.latitude,result_node.longitude,result_node.surface_total,
                     result_node.surface_covered,result_node.bedrooms,result_node.bathrooms,result_node.operation_type,result_node.price,result_node.valor,result_node.valor2)
    valor_result_node = result_node.valor
    valor2_result_node = result_node.valor2

    if op2 == 0:
      dot = to_dot(root2)
      image = Image(data=dot.pipe(format='png'))
      display(image)
      dot2  = to_dot(root)

      print("\nDesea visualizar la ubicación geografica del nodo? Si lo desea digite 'si':")
      re = input()
      if re.lower() == "si":
        print("Localización geografica del nodo: \n")
        mapa_resultante = mapa( result_node)
        display(mapa_resultante)

    elif op2 == 1:
      print("Nivel del nodo: ")
      nivel = find_level(root, result_node.valor, result_node.valor2)
      print(nivel)

    elif op2 == 2:
      print("El factor de balanceo del nodo es: ")
      factor = balance_factor(result_node)
      print(factor)

    elif op2 == 3:
      print("Encontrar el padre del nodo: ")
      nodo_parent = find_parent(root, valor_result_node, valor2_result_node)
      rootP = insert(rootP,nodo_parent.title,nodo_parent.department,nodo_parent.city,nodo_parent.property_type,nodo_parent.latitude,nodo_parent.longitude,nodo_parent.surface_total,
                     nodo_parent.surface_covered,nodo_parent.bedrooms,nodo_parent.bathrooms,nodo_parent.operation_type,nodo_parent.price,nodo_parent.valor,nodo_parent.valor2)

      dot = to_dot(rootP)
      image = Image(data=dot.pipe(format='png'))
      display(image)
      dot2  = to_dot(root)

      print("\nDesea visualizar la ubicación geografica del nodo? Si lo desea digite 'si':")
      re = input()
      if re.lower() == "si":
        print("Localización geografica del nodo: \n")
        mapa_resultante = mapa(nodo_parent)
        display(mapa_resultante)

    elif op2 == 4:
      print("Encontrar el abuelo del nodo: ")
      nodo_grandparent = find_grandparent(root, valor_result_node, valor2_result_node)
      rootA = insert(rootA,nodo_grandparent.title,nodo_grandparent.department,nodo_grandparent.city,nodo_grandparent.property_type,nodo_grandparent.latitude,nodo_grandparent.longitude,nodo_grandparent.surface_total,
                     nodo_grandparent.surface_covered,nodo_grandparent.bedrooms,nodo_grandparent.bathrooms,nodo_grandparent.operation_type,nodo_grandparent.price,nodo_grandparent.valor,nodo_grandparent.valor2)
      dot = to_dot(rootA)
      image = Image(data=dot.pipe(format='png'))
      display(image)
      dot2  = to_dot(root)

      print("\nDesea visualizar la ubicación geografica del nodo? Si lo desea digite 'si':")
      re = input()
      if re.lower() == "si":
        print("Localización geografica del nodo: \n")
        mapa_resultante = mapa(nodo_grandparent)
        display(mapa_resultante)

    elif op2 == 5:
      print("Encontrar el tio del nodo: ")
      nodo_uncle = find_uncle(root, valor_result_node, valor2_result_node)
      rootT = insert(rootT,nodo_uncle.title,nodo_uncle.department,nodo_uncle.city,nodo_uncle.property_type,nodo_uncle.latitude,nodo_uncle.longitude,nodo_uncle.surface_total,
                     nodo_uncle.surface_covered,nodo_uncle.bedrooms,nodo_uncle.bathrooms,nodo_uncle.operation_type,nodo_uncle.price,nodo_uncle.valor,nodo_uncle.valor2)
      dot = to_dot(rootT)
      image = Image(data=dot.pipe(format='png'))
      display(image)
      dot2  = to_dot(root)

      print("\nDesea visualizar la ubicación geografica del nodo? Si lo desea digite 'si':")
      re = input()
      if re.lower() == "si":
        print("Localización geografica del nodo: \n")
        mapa_resultante = mapa(nodo_uncle)
        display(mapa_resultante)

    else:
      print("Digitó una opción no valida.")
   else:
    print("No se encontro el nodo")

  elif op == 5:
    print('Recuerde que al elegir ciudad, la primera palabra de la ciudad comienza en mayuscula!\n')
    print('¿Cuantas metricas desea usar?, elige las que quieras!, pero te recomendamos un maximo 3')
    c_metrica = int(input())
    metricas = db.columns.tolist()
    seleccion = []
    operador = []
    valor = []
    for i in range(c_metrica):
           print(f'Seleccione la métrica {i+1} de {c_metrica}:')
           for j, metrica in enumerate(metricas):
                 print(f'{j+1}. {metrica}')
           seleccion.append(input())
    # Obtener las condiciones de filtrado del usuario
    for metrica in seleccion:
        if metrica == ('property_type' or 'operation_type' or 'city' or 'department' or 'title'):
         print(f'Ingrese el operador de comparación para {metrica} (Debe ser ==):')
         categorico_op = input()
         if categorico_op != '==':
          while categorico_op != '==':
            print('por favor, digite el operador correspondiente')
            categorico_op = input()
         operador.append(categorico_op)
        else:
          print(f'Ingrese el operador de comparación para {metrica} (por ejemplo, >, <, >=, <=, ==):')
          operador.append(input())
    for metrica in seleccion:
        print(f'Ingrese el valor para {metrica}:')
        valor.append(input())
    filtered_nodes = search_metrics(root, seleccion, operador, valor)
    print(filtered_nodes)
    if len(filtered_nodes) == 0:
      print('Ningun nodo cumple con las metricas')
    else:
      root3 = None
      for result_node in filtered_nodes:
         root3 = insert(root3,result_node.title,result_node.department,result_node.city,result_node.property_type,result_node.latitude,result_node.longitude,result_node.surface_total,
                     result_node.surface_covered,result_node.bedrooms,result_node.bathrooms,result_node.operation_type,result_node.price,result_node.valor,result_node.valor2)
      print(f'\nSe hallaron {len(filtered_nodes)} nodos que cumplen con las metricas!')
      print('Qué desea hacer con los nodos:')
      print("0. Visualizar los nodos")
      print('1. Obtener los niveles de los nodos')
      print('2. Obtener los factor de balanceo de los nodos')
      print('3. Encontrar los padre de los nodos')
      print('4. Encontrar los abuelos de los nodos')
      print('5. Encontrar los tíos de los nodos')

      op2 = int(input('Ingrese una opcion'))

      if op2 == 0:

        for result_node in filtered_nodes:
          print(f"Ciudad: {result_node.city}, Tipo de operación: {result_node.operation_type}: Tipo de propiedad: {result_node.property_type}, Precio: {result_node.price}")
        print(a)

        print("Localización geografica de los nodos: \n")

        mapa_resultante = mapa_list(filtered_nodes)
        display(mapa_resultante)

      elif op2 == 1:

       for resultnode in filtered_nodes:
        print("Nivel del nodo: ")
        print((root.height - resultnode.height) + 1)

      elif op2 == 2:
        b = blank()
        print(b)
        for result_node in filtered_nodes:
          print("El factor de balanceo del nodo ", result_node.valor ," es: ")
          factor = balance_factor(result_node)
          print(factor)

      elif op2 == 3:

        print("Encontrar los padres de los nodos: ")
        parent_list = []
        rootP = None
        for result_node in filtered_nodes:
          nodo_parent = find_parent(root, result_node.valor, result_node.valor2)
          parent_list.append(nodo_parent)
          rootP = insert(rootP,nodo_parent.title,nodo_parent.department,nodo_parent.city,nodo_parent.property_type,nodo_parent.latitude,nodo_parent.longitude,nodo_parent.surface_total,
                     nodo_parent.surface_covered,nodo_parent.bedrooms,nodo_parent.bathrooms,nodo_parent.operation_type,nodo_parent.price,nodo_parent.valor,nodo_parent.valor2)
          print(f"Ciudad: {nodo_parent.city}, Tipo de operación: {nodo_parent.operation_type}: Tipo de propiedad: {nodo_parent.property_type}, Precio: {nodo_parent.price}")

        print("Localización geografica de los nodos: \n")

        mapa_resultante = mapa_list(parent_list)
        display(mapa_resultante)

      elif op2 == 4:

        print("Encontrar los abuelos de los nodos: ")
        grandparent_list = []
        rootA = None
        for result_node in filtered_nodes:
          nodo_grandparent = find_parent(root, result_node.valor, result_node.valor2)
          grandparent_list.append(nodo_grandparent)
          rootA = insert(rootA,nodo_grandparent.title,nodo_grandparent.department,nodo_grandparent.city,nodo_grandparent.property_type,nodo_grandparent.latitude,nodo_grandparent.longitude,nodo_grandparent.surface_total,
                     nodo_grandparent.surface_covered,nodo_grandparent.bedrooms,nodo_grandparent.bathrooms,nodo_grandparent.operation_type,nodo_grandparent.price,nodo_grandparent.valor,nodo_grandparent.valor2)
          print(f"Ciudad: {nodo_grandparent.city}, Tipo de operación: {nodo_grandparent.operation_type}: Tipo de propiedad: {nodo_grandparent.property_type}, Precio: {nodo_grandparent.price}")

        print("Localización geografica de los nodos: \n")

        mapa_resultante = mapa_list(grandparent_list)
        display(mapa_resultante)

      elif op2 == 5:

        print("Encontrar los tios de los nodos: ")
        uncle_list = []
        rootT = None
        for result_node in filtered_nodes:
          nodo_uncle = find_parent(root, result_node.valor, result_node.valor2)
          uncle_list.append(nodo_uncle)
          rootT = insert(rootT,nodo_uncle.title,nodo_uncle.department,nodo_uncle.city,nodo_uncle.property_type,nodo_uncle.latitude,nodo_uncle.longitude,nodo_uncle.surface_total,
                     nodo_uncle.surface_covered,nodo_uncle.bedrooms,nodo_uncle.bathrooms,nodo_uncle.operation_type,nodo_uncle.price,nodo_uncle.valor,nodo_uncle.valor2)
          print(f"Ciudad: {nodo_uncle.city}, Tipo de operación: {nodo_uncle.operation_type}: Tipo de propiedad: {nodo_uncle.property_type}, Precio: {nodo_uncle.price}")

        print("Localización geografica de los nodos: \n")

        mapa_resultante = mapa_list(uncle_list)
        display(mapa_resultante)

      else:
        print("Opción no válida")

  elif op == 6:
    niveles = level_order_traversal(root)
    print(niveles)

  elif op == 0:
    print("Ha seleccionado la opción salir")

  else:
    print("Opción no válida")
