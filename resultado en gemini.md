¡Hola! Entiendo perfectamente el objetivo. Vamos a construir **HagaloCRUD**, un proyecto robusto que no solo conecta Flutter con Firebase, sino que utiliza una estructura de **Agentes y Roles** para que los estudiantes comprendan la separación de responsabilidades en el desarrollo de software.

Para este proyecto, interpretaremos "Antigravity" como el concepto de **diseño ligero y desacoplado**, permitiendo que la app "flote" sobre la lógica sin estar pesadamente amarrada a una sola clase.

---

## 1. Estructura de Directorios y Preparación
Primero, organizamos nuestro espacio de trabajo siguiendo tu nomenclatura:

```bash
# Comandos en terminal
mkdir xflutterdominguez0569
cd xflutterdominguez0569
flutter create Hagalocrud
cd Hagalocrud
```

### Estructura del Proyecto (Clean Architecture - Agentes)
Organizaremos el código para que cada parte tenga un **rol** específico:
* **Models:** La estructura de los datos.
* **Services (Agente de Datos):** Habla con Firebase.
* **Providers (Agente de Estado):** Gestiona la lógica de la UI.
* **UI (Widgets):** La cara del proyecto.

---

## 2. Configuración de Firebase y Librerías

### Consola de Firebase
1.  Crea un proyecto en [Firebase Console](https://console.firebase.google.com/).
2.  Crea una base de datos **Cloud Firestore** en modo de prueba.
3.  Crea una colección llamada `herramientas`.
4.  Registra tu app (Android/iOS) y descarga el archivo `google-services.json`.

### Archivo `pubspec.yaml`
Para integrar Firebase, añade estas líneas en la sección `dependencies`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.2
  cloud_firestore: ^4.14.0
  provider: ^6.1.1 # Para el manejo de roles y estados
```
*Ejecuta `flutter pub get` en la terminal para instalar.*

---

## 3. Implementación del Código (El Ecosistema Functional)

### A. El Modelo (Data Skill)
Archivo: `lib/models/herramienta_model.dart`
```dart
class Herramienta {
  String id;
  String nombre;
  double precio;
  int stock;

  Herramienta({required this.id, required this.nombre, required this.precio, required this.stock});

  // Convierte Firestore Document a Objeto Dart
  factory Herramienta.fromMap(Map<String, dynamic> data, String id) {
    return Herramienta(
      id: id,
      nombre: data['nombre'] ?? '',
      precio: (data['precio'] ?? 0).toDouble(),
      stock: data['stock'] ?? 0,
    );
  }

  // Convierte Objeto Dart a Map para Firebase
  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'precio': precio,
      'stock': stock,
    };
  }
}
```

### B. El Agente de Firebase (Role: Data Provider)
Archivo: `lib/services/firebase_service.dart`
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/herramienta_model.dart';

class FirebaseService {
  final CollectionReference collection = FirebaseFirestore.instance.collection('herramientas');

  // CREATE
  Future<void> addHerramienta(String nombre, double precio, int stock) {
    return collection.add({'nombre': nombre, 'precio': precio, 'stock': stock});
  }

  // READ (Stream para tiempo real)
  Stream<List<Herramienta>> getHerramientas() {
    return collection.snapshots().map((snapshot) {
      return snapshot.docs.map((doc) => Herramienta.fromMap(doc.data() as Map<String, dynamic>, doc.id)).toList();
    });
  }

  // UPDATE
  Future<void> updateHerramienta(String id, String nombre, double precio, int stock) {
    return collection.doc(id).update({'nombre': nombre, 'precio': precio, 'stock': stock});
  }

  // DELETE
  Future<void> deleteHerramienta(String id) {
    return collection.doc(id).delete();
  }
}
```

---

## 4. UI: Interfaz con Estilo Atractivo (The "Antigravity" View)
Archivo: `lib/main.dart`
Utilizaremos colores vibrantes como **Deep Purple** y **Amber** para una estética moderna.

```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'services/firebase_service.dart';
import 'models/herramienta_model.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  runApp(HagaloCRUDApp());
}

class HagaloCRUDApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        primarySwatch: Colors.deepPurple,
        useMaterial3: true,
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurpleAccent),
      ),
      home: HomePage(),
    );
  }
}

class HomePage extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("HagaloCRUD - Herramientas"), centerTitle: true),
      body: StreamBuilder<List<Herramienta>>(
        stream: _service.getHerramientas(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          final lista = snapshot.data!;
          return ListView.builder(
            itemCount: lista.length,
            itemBuilder: (context, index) {
              final h = lista[index];
              return Card(
                elevation: 4,
                margin: EdgeInsets.symmetric(horizontal: 10, vertical: 6),
                child: ListTile(
                  leading: CircleAvatar(backgroundColor: Colors.amber, child: Text(h.stock.toString())),
                  title: Text(h.nombre, style: TextStyle(fontWeight: FontWeight.bold)),
                  subtitle: Text("Precio: \$${h.precio}"),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(icon: Icon(Icons.edit, color: Colors.blue), onPressed: () => _showForm(context, h)),
                      IconButton(icon: Icon(Icons.delete, color: Colors.red), onPressed: () => _service.deleteHerramienta(h.id)),
                    ],
                  ),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Colors.amber,
        child: Icon(Icons.add),
        onPressed: () => _showForm(context, null),
      ),
    );
  }

  void _showForm(BuildContext context, Herramienta? herramienta) {
    final nameCtrl = TextEditingController(text: herramienta?.nombre ?? "");
    final priceCtrl = TextEditingController(text: herramienta?.precio.toString() ?? "");
    final stockCtrl = TextEditingController(text: herramienta?.stock.toString() ?? "");

    showModalBottomSheet(
      isScrollControlled: true,
      context: context,
      builder: (_) => Padding(
        padding: EdgeInsets.only(bottom: MediaQuery.of(context).viewInsets.bottom, left: 20, right: 20, top: 20),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(controller: nameCtrl, decoration: InputDecoration(labelText: "Nombre")),
            TextField(controller: priceCtrl, decoration: InputDecoration(labelText: "Precio"), keyboardType: TextInputType.number),
            TextField(controller: stockCtrl, decoration: InputDecoration(labelText: "Stock"), keyboardType: TextInputType.number),
            SizedBox(height: 20),
            ElevatedButton(
              style: ElevatedButton.styleFrom(backgroundColor: Colors.deepPurple, foregroundColor: Colors.white),
              onPressed: () {
                if (herramienta == null) {
                  _service.addHerramienta(nameCtrl.text, double.parse(priceCtrl.text), int.parse(stockCtrl.text));
                } else {
                  _service.updateHerramienta(herramienta.id, nameCtrl.text, double.parse(priceCtrl.text), int.parse(stockCtrl.text));
                }
                Navigator.pop(context);
              },
              child: Text(herramienta == null ? "Crear" : "Actualizar"),
            )
          ],
        ),
      ),
    );
  }
}
```

---

## 5. Metodología de Práctica Guiada (Agentes y Roles)

Para que los estudiantes dominen el flujo, aplicamos la **Metodología de Roles de Desarrollo**:

1.  **Rol de Arquitecto (Estructura):** Define la carpeta `xflutterdominguez0569`. No se escribe código sin tener las carpetas listas.
2.  **Agente de Configuración (Cloud):** Su única misión es conectar la Consola de Firebase con el proyecto. Skill: Manejo de `google-services.json` y `pubspec.yaml`.
3.  **Agente de Datos (Modelo):** Define cómo luce una "Herramienta". Sin este agente, la app no sabe qué está guardando.
4.  **Agente de Comunicación (CRUD Service):** Es el traductor. Toma las peticiones de la app y las lleva a la nube de Firebase.
5.  **Agente de Experiencia de Usuario (UI):** Implementa el diseño "Antigravity" (ligero, intuitivo). Usa widgets como `StreamBuilder` para que la app responda al instante (reactiva).

### Flujo de Trabajo para el Estudiante:
* **Paso 1:** Definir el modelo (Skill: Abstracción).
* **Paso 2:** Crear el Servicio (Skill: Integración API).
* **Paso 3:** Consumir el servicio en la UI (Skill: Frontend Reactivo).

Este enfoque asegura que si algo falla, el estudiante sepa qué "Agente" debe revisar. Si no guarda, es el Agente de Comunicación; si se ve mal, es el Agente de UI.

¿Te gustaría que profundicemos en la creación de validaciones específicas para los campos de precio y stock?
