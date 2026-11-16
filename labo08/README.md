# 📘 Laboratorio 08: Transformación a MVVM de la Lista de la Compra

## 📝 Objetivo

En este laboratorio transformarás tu aplicación de la **Lista de la Compra** (desarrollada en los laboratorios 07 y 08) para que utilice el patrón arquitectónico **MVVM (Model–View–ViewModel)** con **estado observable** en Jetpack Compose.

## 🛠️ ¿Qué tienes que hacer?

1. **Crear un ViewModel** que gestione la lista de la compra (`ShoppingItem`), mantenga el estado con `MutableState` o `StateFlow` y exponga la lista para que la vista pueda observarla.
2. **Mover toda la lógica** de añadir, borrar o modificar elementos desde la UI al ViewModel.
3. **Actualizar tu pantalla** para que observe el estado del ViewModel y se recomponga automáticamente cuando cambie la lista.

📌 *En resumen:* debes **refactorizar tu aplicación actual del Lab 7 para que siga MVVM**, manteniendo el mismo comportamiento funcional, pero con una arquitectura limpia y separada por capas.

## Pasos
Lo primero de todo es decidir si vas a trabajar sobre tu práctica anterior, o vas a utilizar la solución propuesta por los docentes. Dicha solución la tienes a continuación:

<details>
  <summary>Solución labo 07</summary>
<br>

```kotlin
package es.uva.inf5g.psm.shoppinglist

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import es.uva.inf5g.psm.shoppinglist.ui.theme.ShoppingListTheme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            ShoppingListTheme {
                Scaffold { paddingValues ->
                    ShoppingListScreen(paddingValues)
                }
            }
        }
    }
}

data class ShoppingItem(
    val id: Int,
    val name: String,
    val quantity: Int,
    val isEditing: Boolean = false
)

@Composable
fun ShoppingListScreen(paddingValues: PaddingValues) {
    var items by remember { mutableStateOf(sampleItems) }
    var showAddDialog by remember { mutableStateOf(false) }

    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
            .fillMaxWidth()
            .background(Color(0xFFF7F9FC))
            .padding(24.dp)
    ) {
        Text(
            text = "🛒 Lista de Compras",
            fontSize = 26.sp,
            fontWeight = FontWeight.Bold,
            color = MaterialTheme.colorScheme.primary,
            modifier = Modifier.padding(vertical = 16.dp)
        )

        AddItemButton { showAddDialog = true }

        if (showAddDialog) {
            ShoppingItemDialog(
                onDismiss = { showAddDialog = false },
                onConfirm = { name, quantity ->
                    items = items + ShoppingItem(items.size + 1, name, quantity)
                    showAddDialog = false
                }
            )
        }

        LazyColumn(
            modifier = Modifier
                .fillMaxSize()
                .padding(top = 8.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            items(items) { item ->
                if (item.isEditing) {
                    ShoppingItemEditor(
                        item = item,
                        onEditComplete = { name, quantity ->
                            items = items.map {
                                if (it.id == item.id) it.copy(name = name, quantity = quantity, isEditing = false)
                                else it.copy(isEditing = false)
                            }
                        }
                    )
                } else {
                    ShoppingListItem(
                        item = item,
                        onEditClick = {
                            items = items.map { it.copy(isEditing = it.id == item.id) }
                        },
                        onDeleteClick = { items = items - item }
                    )
                }
            }
        }
    }
}

@Composable
fun AddItemButton(onClick: () -> Unit) {
    Button(
        onClick = onClick,
        colors = ButtonDefaults.buttonColors(containerColor = Color(0xFF2196F3), contentColor = Color.White),
        shape = RoundedCornerShape(50),
        modifier = Modifier
            .padding(vertical = 8.dp)
            .height(50.dp)
            .width(200.dp)
            .shadow(4.dp, RoundedCornerShape(50))
    ) {
        Text("➕ Añadir Item", fontSize = 18.sp, fontWeight = FontWeight.SemiBold)
    }
}

@Composable
fun ShoppingItemDialog(onDismiss: () -> Unit, onConfirm: (String, Int) -> Unit) {
    var name by remember { mutableStateOf("") }
    var quantity by remember { mutableStateOf("") }
    var error by remember { mutableStateOf("") }

    AlertDialog(
        onDismissRequest = onDismiss,
        title = { Text("Añadir un nuevo artículo") },
        text = {
            Column {
                OutlinedTextField(
                    value = name,
                    onValueChange = { name = it; error = "" },
                    label = { Text("Nombre del artículo") },
                    singleLine = true,
                    modifier = Modifier.fillMaxWidth().padding(4.dp)
                )
                OutlinedTextField(
                    value = quantity,
                    onValueChange = { quantity = it; error = "" },
                    label = { Text("Cantidad") },
                    singleLine = true,
                    modifier = Modifier.fillMaxWidth().padding(4.dp)
                )
                if (error.isNotEmpty()) {
                    Text(error, color = Color.Red, fontSize = 13.sp, modifier = Modifier.padding(start = 8.dp, top = 4.dp))
                }
            }
        },
        confirmButton = {
            Button(onClick = {
                val q = quantity.toIntOrNull()
                when {
                    name.isBlank() -> error = "El nombre no puede estar vacío"
                    q == null || q <= 0 -> error = "La cantidad debe ser mayor que 0"
                    else -> onConfirm(name, q)
                }
            }) { Text("Aceptar") }
        },
        dismissButton = { OutlinedButton(onClick = onDismiss) { Text("Cancelar") } }
    )
}

@Composable
fun ShoppingListItem(item: ShoppingItem, onEditClick: () -> Unit, onDeleteClick: () -> Unit) {
    Card(
        modifier = Modifier.fillMaxWidth(),
        shape = RoundedCornerShape(20),
        colors = CardDefaults.cardColors(containerColor = Color.White),
        elevation = CardDefaults.cardElevation(4.dp)
    ) {
        Row(modifier = Modifier.padding(12.dp).fillMaxWidth(), verticalAlignment = Alignment.CenterVertically) {
            Column(modifier = Modifier.weight(1f)) {
                Text(item.name, fontSize = 18.sp, fontWeight = FontWeight.Medium, color = Color(0xFF212121))
                Text("Cantidad: ${item.quantity}", fontSize = 14.sp, color = Color(0xFF757575))
            }
            IconButton(onClick = onEditClick) { Icon(painter = painterResource(R.drawable.baseline_edit_24), contentDescription = "Editar", tint = Color(0xFF0288D1)) }
            IconButton(onClick = onDeleteClick) { Icon(painter = painterResource(R.drawable.baseline_delete_24), contentDescription = "Eliminar", tint = Color(0xFFD32F2F)) }
        }
    }
}

@Composable
fun ShoppingItemEditor(item: ShoppingItem, onEditComplete: (String, Int) -> Unit) {
    var name by remember { mutableStateOf(item.name) }
    var quantity by remember { mutableStateOf(item.quantity.toString()) }
    var error by remember { mutableStateOf("") }

    Card(modifier = Modifier.fillMaxWidth(), shape = RoundedCornerShape(16.dp), elevation = CardDefaults.cardElevation(4.dp)) {
        Column(modifier = Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(8.dp)) {
            OutlinedTextField(value = name, onValueChange = { name = it; error = "" }, label = { Text("Nombre del artículo") }, modifier = Modifier.fillMaxWidth())
            OutlinedTextField(value = quantity, onValueChange = { quantity = it; error = "" }, label = { Text("Cantidad") }, modifier = Modifier.fillMaxWidth())
            if (error.isNotEmpty()) Text(error, color = Color.Red, fontSize = 13.sp)
            Button(onClick = {
                val q = quantity.toIntOrNull()
                when {
                    name.isBlank() -> error = "El nombre no puede estar vacío"
                    q == null || q <= 0 -> error = "La cantidad debe ser mayor que 0"
                    else -> onEditComplete(name, q)
                }
            }, modifier = Modifier.align(Alignment.End)) { Text("Guardar") }
        }
    }
}

private val sampleItems = listOf(
    ShoppingItem(1, "Unicornio Inflable", 1),
    ShoppingItem(2, "Sombrero para Gatos", 3),
    ShoppingItem(3, "Bocadillo de Aire Fresco", 1),
    ShoppingItem(4, "Teletransportador Portátil", 1),
    ShoppingItem(5, "Espejo de Espejismos", 2),
    ShoppingItem(6, "Disfraz de Alienígena", 4),
    ShoppingItem(7, "Nariz Postiza para Perros", 2),
    ShoppingItem(8, "Peluche de Monstruo del Lago Ness", 1),
    ShoppingItem(9, "Taza que Te Grita en la Mañana", 1),
    ShoppingItem(10, "Reloj de Sol de Bolsillo", 1),
    ShoppingItem(11, "Café que No te Despierta", 3),
    ShoppingItem(12, "Calcetines Antigravedad", 2),
    ShoppingItem(13, "Bufanda de Inexistencia", 1),
    ShoppingItem(14, "Agua Embotellada para Peces", 5),
    ShoppingItem(15, "Sándwich de Energía Infinita", 1),
    ShoppingItem(16, "Almohada para Soñar Despierto", 2),
    ShoppingItem(17, "Paraguas que Llueve", 3),
    ShoppingItem(18, "Escalera para Alcanzar las Estrellas", 1),
    ShoppingItem(19, "Mapa del Tesoro Ficticio", 1),
    ShoppingItem(20, "Espejo que Muestra la Juventud", 2)
)
```

</details>
<br>

## Model
Una vez decidido, sacamos del MainActivity la definición del modelo, llevándolo al fichero ShoppinItemModel.

<details>
  <summary>Model</summary>
<br>

```kotlin
package es.uva.inf5g.psm.shoppinglist

data class ShoppingItem(
    val id: Int,
    val name: String,
    val quantity: Int,
    val isEditing: Boolean = false
)
```

</details>
<br>

## Repository
Con el modelo preparado, creamos el fichero ShoppingRepository, donde meteremos la funcionalidad del repositorio

<details>
  <summary>Repository</summary>
<br>

```kotlin
package es.uva.inf5g.psm.shoppinglist

class ShoppingRepository {
    private var _items = listOf(
        ShoppingItem(1, "Unicornio Inflable", 1),
        ShoppingItem(2, "Sombrero para Gatos", 3),
        ShoppingItem(3, "Bocadillo de Aire Fresco", 1),
        ShoppingItem(4, "Teletransportador Portátil", 1),
        ShoppingItem(5, "Espejo de Espejismos", 2),
        ShoppingItem(6, "Disfraz de Alienígena", 4),
        ShoppingItem(7, "Nariz Postiza para Perros", 2),
        ShoppingItem(8, "Peluche de Monstruo del Lago Ness", 1),
        ShoppingItem(9, "Taza que Te Grita en la Mañana", 1),
        ShoppingItem(10, "Reloj de Sol de Bolsillo", 1),
        ShoppingItem(11, "Café que No te Despierta", 3),
        ShoppingItem(12, "Calcetines Antigravedad", 2),
        ShoppingItem(13, "Bufanda de Inexistencia", 1),
        ShoppingItem(14, "Agua Embotellada para Peces", 5),
        ShoppingItem(15, "Sándwich de Energía Infinita", 1),
        ShoppingItem(16, "Almohada para Soñar Despierto", 2),
        ShoppingItem(17, "Paraguas que Llueve", 3),
        ShoppingItem(18, "Escalera para Alcanzar las Estrellas", 1),
        ShoppingItem(19, "Mapa del Tesoro Ficticio", 1),
        ShoppingItem(20, "Espejo que Muestra la Juventud", 2)
    )

    fun getItems(): List<ShoppingItem> = _items

    fun createItem(name: String, quantity: Int): ShoppingItem {
        val newId = (_items.maxOfOrNull { it.id } ?: 0) + 1
        return ShoppingItem(newId, name, quantity)
    }

    fun addItem(item: ShoppingItem) {
        _items = _items + item
    }

    fun removeItem(item: ShoppingItem) {
        _items = _items.filter { it.id != item.id }
    }

    fun updateItem(updatedItem: ShoppingItem) {
        _items = _items.map { if (it.id == updatedItem.id) updatedItem else it }
    }

    fun editItem(itemToEdit: ShoppingItem) {
        _items = _items.map { item ->
            if (item.id == itemToEdit.id)
                item.copy(isEditing = true)
            else
                item
        }
    }
}
```

</details>
<br>

## ViewModel
Aquí vamos a vais a tener que trabajar un poco por vuestra cuenta... Crearemos el fichero ShoppingViewModel, y le meteremos la funcionalidad para trabajar sobre el repositorio.

Os dejo la definición de las variables de estado y las cabeceras de las funciones. Os dejo también la función addItem completa para que la uséis como ejemplo.

Tenéis que completar la lógica de negocio usando las funciones del repositorio.

<details>
  <summary>ViewModel</summary>
<br>

```kotlin
package es.uva.inf5g.psm.shoppinglist

import androidx.compose.runtime.State
import androidx.compose.runtime.mutableStateOf
import androidx.lifecycle.ViewModel

class ShoppingViewModel : ViewModel() {
    private val repository = ShoppingRepository()
    private val _items = mutableStateOf(repository.getItems())
    val items: State<List<ShoppingItem>> = _items

    fun addItem(name: String, quantity: Int) {
        val newItem = repository.createItem(name, quantity)
        repository.addItem(newItem)
        _items.value = repository.getItems()
    }


    fun removeItem(item: ShoppingItem) {
        // Incluir lógica de negocio
    }

    fun editItem(item: ShoppingItem, name: String, quantity: Int) {
        // Incluir lógica de negocio
    }

    fun editItem(item: ShoppingItem){
        // Incluir lógica de negocio
    }
}
```

</details>
<br>

## MainActivity
Por último, tendremos que ajustar el MainActivity para utilizar el ViewModel programado.

Os incluyo algunos cachos del código con la solución para que lo uséis como ejemplo.

<details>
  <summary>MainActivity</summary>
<br>

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            val shoppingViewModel: ShoppingViewModel by viewModels()

            ShoppingListTheme {
                ShoppingListScreen(viewModel = shoppingViewModel)
            }
        }
    }
}
```

</details>
<br>

<details>
  <summary>Solución apartado showAddDialog</summary>
<br>

```kotlin
        if (showAddDialog) {
            ShoppingItemDialog(
                onDismiss = { showAddDialog = false },
                onConfirm = { name, quantity ->
                    viewModel.addItem(name, quantity)
                    showAddDialog = false
                }
            )
        }
```

</details>
<br>

<details>
  <summary>LazyColumn</summary>
<br>

```kotlin
        LazyColumn(
            modifier = Modifier
                .fillMaxSize()
                .padding(top = 8.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            items(viewModel.items.value) { item ->
                if (item.isEditing) {
                    ShoppingItemEditor(
                        item = item,
                        onEditComplete = { name, quantity ->
                            viewModel.editItem(item, name, quantity)
                        }
                    )
                } else {
                    ShoppingListItem(
                        item = item,
                        onEditClick = {
                            viewModel.editItem(item)
                        },
                        onDeleteClick = { viewModel.removeItem(item)}
                    )
                }
            }
        }
```

</details>
<br>

## ¿Probamos?

Ya sólo nos queda probar el proyecto!!!

Probad a guardar sin introducir información, a meter texto donde debería ir un número... En fin, probad y ved cómo funciona el control de errores.