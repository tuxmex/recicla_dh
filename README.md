1. Objetivo: Construir una app Android (Jetpack Compose) que permita a ciudadanos registrar entregas de material reciclable (papel, vidrio, plástico), acumular puntos por cada registro y canjear esos puntos por becas o apoyos otorgados por la presidencia municipal de Dolores Hidalgo.
Ejemplo cotidiano: Ana lleva botellas plásticas al centro de acopio. Ella escanea/adjunta una foto y registra 3 kg de PET → la app calcula 30 puntos → esos puntos se suman a su cuenta → al llegar a 500 puntos puede solicitar una beca municipal o un apoyo para compra de útiles escolares.
## Actores / roles:

* Usuario (ciudadano): registra reciclaje, consulta puntos, canjea.
* Operador del centro de acopio / municipal: valida entregas, acepta/declina registros, administra recompensas.
* Administrador municipal: define recompensas, gestiona políticas.

2. Alcance funcional (MVP — mínimo viable)

* Registro e inicio de sesión (Firebase Auth: correo/contraseña y Google).
* Perfil de usuario con puntos acumulados.
* Registrar entrega de material (tipo, peso, foto, ubicación opcional).
* Validación por operador (aceptar/rechazar).
* Lista de recompensas (becas/apoyos) con coste en puntos.
* Canje de recompensa: crear solicitud de canje y disminuir puntos cuando es aprobada.
* Historial de entregas y canjes.
* Panel administrativo (web o dentro de app) para aprobar canjes, crear recompensas.
3. Arquitectura propuesta y buenas prácticas (docente)
* Arquitectura: MVVM (ViewModel + Repository) + Repository pattern para desacoplar Firebase.
* Inyección de dependencias: Hilt.
* UI: Jetpack Compose + Navigation Compose.
* Data: Firebase Authentication, Firestore (colecciones para Users, Deliveries, Rewards, Redemptions), Firebase Storage para fotos.
* Concurrencia: Kotlin Coroutines + Flow.
* Patrones: Single Source of Truth (ViewModel expone StateFlows/LiveData), Repository maneja mapeo a modelos de dominio.
* Seguridad: Reglas de Firestore + Storage; Validaciones de back-end preferible (Cloud Functions) para evitar fraude (p. ej. para asignar puntos sólo si operador valida).
* Testing: Unit tests de ViewModel, pruebas instrumentadas para navegación y UI con Compose Test.
4. Preparación del entorno (paso a paso)

1. Android Studio: usar una versión reciente (Arctic Fox o superior; idealmente Electric Eel o más reciente).

2. Crear proyecto: New Project → Empty Compose Activity → Kotlin → Minimum SDK 23+.

3. Configurar Firebase:
   * Crear proyecto en Firebase Console (ej. recicla-dolores).
   * Agregar app Android: paquete mx.edu.utng.arg.recicladh.
   * Descargar google-services.json y colocarlo en app/.
   * Habilitar Authentication: Email/Password y Google Sign-In.
   * Crear Firestore en modo de producción (o modo de prueba durante desarrollo).
   * Habilitar Firebase Storage.

# Manual Completo para Docentes: App de Reciclaje con Jetpack Compose + Firebase

📚 Guía Detallada para Enseñar Desarrollo Android Moderno
PARTE 1: FUNDAMENTOS Y CONCEPTOS (Para explicar a los estudiantes)
1.1 ¿Qué vamos a construir? (Explicación con ejemplos cotidianos)

Imagina que Dolores Hidalgo quiere ser una ciudad más limpia y sustentable. La presidencia municipal tiene un problema: ¿cómo motivar a los ciudadanos a reciclar más?

Solución tradicional (sin app):

    Doña María lleva sus botellas al centro de acopio
    Le dan un papelito con puntos escritos a mano
    Pierde el papelito o se le olvida cuántos puntos tiene
    Nunca canjea sus recompensas porque es muy complicado

Solución con nuestra app:

    Doña María registra su entrega desde su celular
    Toma foto de sus botellas (3 kg de PET)
    La app automáticamente registra 30 puntos (10 puntos por kg)
    Un operador del municipio valida la entrega
    Los puntos se suman automáticamente
    Cuando llega a 500 puntos, puede canjear una beca escolar para su nieto
    Todo queda registrado digitalmente, sin papeles perdidos

¿Por qué es importante para los estudiantes aprender esto?

    Combina tecnología con impacto social real
    Usa herramientas profesionales actuales (Compose, Firebase)
    Resuelve un problema real de su comunidad
    Pueden poner este proyecto en su portafolio profesional

PARTE 2: CONCEPTOS TÉCNICOS EXPLICADOS (Fundamento teórico)

2.1 ¿Qué es Jetpack Compose?

Explicación simple: Compose es la forma moderna de crear interfaces en Android. Antes dibujábamos pantallas con XML (como dibujar con regla y compás). Ahora con Compose programamos interfaces con Kotlin (como usar herramientas de diseño digital).

Analogía para estudiantes:

    XML (forma antigua): Como escribir una carta a mano, debes describir exactamente dónde va cada palabra
    Compose (forma moderna): Como usar WhatsApp, escribes y el programa automáticamente acomoda todo

Ejemplo visual:

kotlin

// Forma antigua (XML + código)
// activity_main.xml:
<TextView 
    android:text="Hola"
    android:textSize="24sp" />

// MainActivity.kt:
val textView = findViewById<TextView>(R.id.textView)
textView.text = "Hola desde código"

// Forma moderna (solo Compose):
@Composable
fun Saludo() {
    Text(
        text = "Hola desde Compose",
        fontSize = 24.sp
    )
}

**Ventajas de Compose:**
1. **Menos código**: Una pantalla que tomaba 100 líneas de XML ahora toma 30 de Compose
2. **Más intuitivo**: Si sabes Kotlin, ya sabes Compose
3. **Reactividad automática**: Cuando cambian los datos, la UI se actualiza sola
4. **Reutilización**: Creas componentes como bloques de LEGO que puedes usar en cualquier lugar

---

### 2.2 ¿Qué es Firebase?

**Explicación simple:**
Firebase es como "tener un servidor completo sin tener que comprar o programar un servidor". Google nos da las herramientas listas para:
- Guardar usuarios (Authentication)
- Guardar datos (Firestore - base de datos en la nube)
- Guardar archivos/fotos (Storage)
- Enviar notificaciones (Cloud Messaging)

**Analogía para estudiantes:**
- **Servidor tradicional**: Como construir tu propia casa desde cero (comprar terreno, contratar albañiles, instalar luz, agua...)
- **Firebase**: Como rentar un departamento amueblado (todo está listo, solo te mudas y usas)

**Componentes que usaremos:**

| Componente | ¿Qué hace? | Ejemplo en nuestra app |
|------------|-----------|------------------------|
| **Authentication** | Maneja usuarios y contraseñas | Registro de Doña María con su correo |
| **Firestore** | Base de datos NoSQL | Guardar puntos, entregas, recompensas |
| **Storage** | Almacena archivos | Guardar fotos de las botellas recicladas |
| **Cloud Functions** | Código que corre en la nube | Validar que los puntos sean correctos |

---

### 2.3 Arquitectura MVVM (Modelo-Vista-VistaModelo)

**Explicación simple:**
MVVM es una forma de organizar el código para que sea fácil de entender, probar y mantener. Es como organizar una cocina profesional.

**Analogía de restaurante:**
```
🍽️ VISTA (View - Jetpack Compose)
   ↓ "El mesero - solo muestra y recibe órdenes"
   ↓
🧠 VIEWMODEL 
   ↓ "El chef - prepara todo, conoce las reglas del restaurante"
   ↓
📦 REPOSITORIO
   ↓ "El almacén - sabe dónde conseguir ingredientes"
   ↓
☁️ FIREBASE (o cualquier fuente de datos)
   "El proveedor - tiene todos los ingredientes"

Ejemplo concreto:
kotlin

// ❌ CÓDIGO MALO (todo mezclado):
@Composable
fun PantallaPuntos() {
    val firestore = FirebaseFirestore.getInstance()
    var puntos by remember { mutableStateOf(0) }
    
    // Lógica de negocio mezclada con UI
    LaunchedEffect(Unit) {
        firestore.collection("users")
            .document("user123")
            .get()
            .addOnSuccessListener { 
                puntos = it.getLong("points")?.toInt() ?: 0 
            }
    }
    
    Text("Puntos: $puntos")
}

// ✅ CÓDIGO BUENO (MVVM):

// 1. MODELO (Data class)
data class Usuario(
    val id: String,
    val nombre: String,
    val puntos: Int
)

// 2. REPOSITORIO (acceso a datos)
class UsuarioRepository(
    private val firestore: FirebaseFirestore
) {
    suspend fun obtenerUsuario(id: String): Usuario {
        val doc = firestore.collection("users")
            .document(id)
            .get()
            .await()
        
        return Usuario(
            id = doc.id,
            nombre = doc.getString("nombre") ?: "",
            puntos = doc.getLong("puntos")?.toInt() ?: 0
        )
    }
}

// 3. VIEWMODEL (lógica de negocio)
@HiltViewModel
class PuntosViewModel @Inject constructor(
    private val repository: UsuarioRepository
) : ViewModel() {
    
    private val _usuario = MutableStateFlow<Usuario?>(null)
    val usuario: StateFlow<Usuario?> = _usuario.asStateFlow()
    
    fun cargarUsuario(id: String) {
        viewModelScope.launch {
            _usuario.value = repository.obtenerUsuario(id)
        }
    }
}

// 4. VISTA (solo UI)
@Composable
fun PantallaPuntos(
    viewModel: PuntosViewModel = hiltViewModel()
) {
    val usuario by viewModel.usuario.collectAsState()
    
    LaunchedEffect(Unit) {
        viewModel.cargarUsuario("user123")
    }
    
    usuario?.let {
        Text("Puntos: ${it.puntos}")
    }
}
```

**¿Por qué es mejor el código bueno?**
1. **Separación de responsabilidades**: Cada parte hace solo una cosa
2. **Testeable**: Puedes probar el ViewModel sin abrir la app
3. **Reutilizable**: El mismo ViewModel puede usarse en diferentes pantallas
4. **Mantenible**: Si cambias Firebase por otra base de datos, solo cambias el Repository

---

## **PARTE 3: CONFIGURACIÓN DEL PROYECTO (Paso a Paso Detallado)**

### 3.1 Crear el proyecto en Android Studio

**Paso 1: Abrir Android Studio**
```
1. File → New → New Project
2. Seleccionar "Empty Activity" (Compose)
3. Configurar:
   - Name: ReciclaDoloreFIRST
   - Package name: mx.edu.utng.reciclaDH
   - Save location: Donde guardes tus proyectos
   - Language: Kotlin
   - Minimum SDK: API 24 (Android 7.0) - cubre 95% de dispositivos
   - Build configuration language: Kotlin DSL (build.gradle.kts)
4. Finish
```

---

### 3.2 Configurar Firebase (Detallado con capturas conceptuales)

**Paso 1: Crear proyecto en Firebase Console**
```
1. Ir a https://console.firebase.google.com
2. Click en "Agregar proyecto"
3. Nombre: "ReciclaDolorFIRSTes Hidalgo"
4. (Opcional) Habilitar Google Analytics
5. Crear proyecto
```

**Paso 2: Agregar app Android a Firebase**
```
1. En tu proyecto Firebase → Click "Android"
2. Registrar app:
   - Nombre del paquete: mx.edu.utng.reciclaDH
     (debe coincidir EXACTAMENTE con tu proyecto)
   - (Opcional) Sobrenombre: ReciclaApp
   - (Opcional) SHA-1: Lo necesitarás después para Google Sign-In
     
   Para obtener SHA-1:
   En Android Studio → Terminal → ejecutar:
   ./gradlew signingReport
   
   Buscar línea que dice "SHA1:" y copiar el código
3. Descargar google-services.json
4. Colocar archivo en: app/google-services.json
```
**Paso 3: Configurar archivos Gradle

📁 build.gradle.kts (Project level)
kotlin

// Top-level build file
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
    
    // Agregar plugin de Google Services
    id("com.google.gms.google-services") version "4.4.0" apply false
    
    // Hilt para inyección de dependencias
    id("com.google.dagger.hilt.android") version "2.48" apply false
}

📁 build.gradle.kts (Module :app)

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
    
    // Plugins necesarios
    id("com.google.gms.google-services")
    id("com.google.dagger.hilt.android")
    id("kotlin-kapt")
}

android {
    namespace = "mx.edu.utng.reciclaDH"
    compileSdk = 34

    defaultConfig {
        applicationId = "mx.edu.utng.reciclaDH"
        minSdk = 24
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
        vectorDrawables {
            useSupportLibrary = true
        }
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }
    
    kotlinOptions {
        jvmTarget = "17"
    }
    
    buildFeatures {
        compose = true
    }
    
    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.3"
    }
    
    packaging {
        resources {
            excludes += "/META-INF/{AL2.0,LGPL2.1}"
        }
    }
}

dependencies {
    // Core Android
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.6.2")
    implementation("androidx.activity:activity-compose:1.8.1")
    
    // Compose BOM (Bill of Materials - maneja versiones automáticamente)
    implementation(platform("androidx.compose:compose-bom:2023.10.01"))
    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.ui:ui-graphics")
    implementation("androidx.compose.ui:ui-tooling-preview")
    implementation("androidx.compose.material3:material3")
    
    // Navigation Compose
    implementation("androidx.navigation:navigation-compose:2.7.5")
    
    // Firebase BOM
    implementation(platform("com.google.firebase:firebase-bom:32.5.0"))
    implementation("com.google.firebase:firebase-auth-ktx")
    implementation("com.google.firebase:firebase-firestore-ktx")
    implementation("com.google.firebase:firebase-storage-ktx")
    implementation("com.google.firebase:firebase-analytics-ktx")
    
    // Google Sign-In
    implementation("com.google.android.gms:play-services-auth:20.7.0")
    
    // Hilt (Inyección de dependencias)
    implementation("com.google.dagger:hilt-android:2.48")
    kapt("com.google.dagger:hilt-android-compiler:2.48")
    implementation("androidx.hilt:hilt-navigation-compose:1.1.0")
    
    // Coil (Cargar imágenes)
    implementation("io.coil-kt:coil-compose:2.5.0")
    
    // Coroutines
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-play-services:1.7.3")
    
    // Testing
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
    androidTestImplementation(platform("androidx.compose:compose-bom:2023.10.01"))
    androidTestImplementation("androidx.compose.ui:ui-test-junit4")
    debugImplementation("androidx.compose.ui:ui-tooling")
    debugImplementation("androidx.compose.ui:ui-test-manifest")
}

// Necesario para Kapt (procesamiento de anotaciones de Hilt)
kapt {
    correctErrorTypes = true
}
```

Explicación para estudiantes - ¿Qué hace cada dependencia?

Dependencia	¿Para qué sirve?	Ejemplo en nuestra app
firebase-auth-ktx	Maneja login/registro	Cuando Doña María crea su cuenta
firebase-firestore-ktx	Base de datos en la nube	Guardar puntos y entregas
firebase-storage-ktx	Guardar archivos	Subir fotos de botellas
hilt-android	Inyección de dependencias	Compartir Repository entre pantallas
navigation-compose	Navegar entre pantallas	Ir de Login → Inicio → Perfil
coil-compose	Mostrar imágenes desde internet	Mostrar foto de la entrega
kotlinx-coroutines	Operaciones asíncronas	Cargar datos sin congelar la app

3.3 Configurar Hilt (Inyección de Dependencias)

¿Qué es la inyección de dependencias? (Explicación simple)

Imagina que estás cocinando:

    Sin inyección: Cada vez que cocinas, vas al supermercado, compras ingredientes, regresas, cocinas
    Con inyección: Tienes un refrigerador bien surtido, solo tomas lo que necesitas

En código:
kotlin
```
// ❌ Sin inyección (mal):
class PantallaPerfil {
    private val firestore = FirebaseFirestore.getInstance() // Creamos instancia aquí
    private val repository = UsuarioRepository(firestore) // Y aquí
    
    // Problema: Difícil de probar, difícil de reutilizar
}

// ✅ Con inyección (bien):
@HiltViewModel
class PerfilViewModel @Inject constructor(
    private val repository: UsuarioRepository // Hilt nos lo da automáticamente
) : ViewModel() {
    // Hilt se encarga de crear FirebaseFirestore y UsuarioRepository
}
```
Configurar Hilt - Paso a Paso:

1. Crear clase Application:

📁 app/src/main/java/mx/edu/utng/reciclaDH/ReciclaApp.kt

```kotlin
package mx.edu.utng.reciclaDH

import android.app.Application
import dagger.hilt.android.HiltAndroidApp

/**
 * Clase principal de la aplicación.
 * @HiltAndroidApp inicializa Hilt para toda la app.
 * 
 * Es como el "cerebro" que sabe cómo crear todas las dependencias.
 */
@HiltAndroidApp
class ReciclaApp : Application() {
    override fun onCreate() {
        super.onCreate()
        // Aquí puedes inicializar cosas que necesita toda la app
        // Por ejemplo, configurar logging, analytics, etc.
    }
}
```
2. Registrar la Application en AndroidManifest.xml:

📁 app/src/main/AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <!-- Permisos necesarios -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" 
        android:maxSdkVersion="32" />
    <uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />

    <application
        android:name=".ReciclaApp"
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.ReciclaDolorFIRSTes"
        tools:targetApi="31">
        
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:theme="@style/Theme.ReciclaDolorFIRSTes">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

3. Crear módulos de Hilt para proveer dependencias:

📁 app/src/main/java/mx/edu/utng/reciclaDH/di/AppModule.kt

```kotlin
package mx.edu.utng.reciclaDH.di

import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.firestore.FirebaseFirestore
import com.google.firebase.storage.FirebaseStorage
import dagger.Module
import dagger.Provides
import dagger.hilt.InstallIn
import dagger.hilt.components.SingletonComponent
import javax.inject.Singleton

/**
 * Módulo de Hilt que provee dependencias de Firebase.
 * 
 * Piensa en esto como una "fábrica" que sabe cómo crear objetos.
 * @Module = "Esto es una fábrica"
 * @InstallIn(SingletonComponent::class) = "Crear UNA SOLA instancia para toda la app"
 * @Provides = "Método que construye un objeto"
 * @Singleton = "Solo crear una vez, reutilizar siempre"
 */
@Module
@InstallIn(SingletonComponent::class)
object AppModule {
    
    /**
     * Provee instancia de FirebaseAuth.
     * Hilt llamará este método automáticamente cuando alguien necesite FirebaseAuth.
     */
    @Provides
    @Singleton
    fun provideFirebaseAuth(): FirebaseAuth {
        return FirebaseAuth.getInstance()
    }
    
    /**
     * Provee instancia de FirebaseFirestore (base de datos).
     */
    @Provides
    @Singleton
    fun provideFirestore(): FirebaseFirestore {
        return FirebaseFirestore.getInstance().apply {
            // Opcional: configurar Firestore
            firestoreSettings = com.google.firebase.firestore.FirebaseFirestoreSettings.Builder()
                .setPersistenceEnabled(true) // Datos disponibles offline
                .build()
        }
    }
    
    /**
     * Provee instancia de FirebaseStorage (para fotos).
     */
    @Provides
    @Singleton
    fun provideStorage(): FirebaseStorage {
        return FirebaseStorage.getInstance()
    }
}
```

---

## **PARTE 4: ESTRUCTURA DEL PROYECTO (Organización de carpetas)**

Una buena organización facilita que los estudiantes encuentren el código:
```
app/src/main/java/mx/edu/utng/reciclaDH/
│
├── 📁 data/                          # Capa de DATOS
│   ├── 📁 model/                     # Modelos de datos
│   │   ├── Usuario.kt
│   │   ├── Entrega.kt
│   │   ├── Recompensa.kt
│   │   └── Canje.kt
│   │
│   ├── 📁 repository/                # Repositorios (acceso a Firebase)
│   │   ├── AuthRepository.kt
│   │   ├── UsuarioRepository.kt
│   │   ├── EntregaRepository.kt
│   │   ├── RecompensaRepository.kt
│   │   └── CanjeRepository.kt
│   │
│   └── 📁 remote/                    # DTOs y mappers (opcional)
│       └── FirestoreMapper.kt
│
├── 📁 di/                            # Inyección de dependencias
│   └── AppModule.kt
│
├── 📁 ui/                            # Capa de PRESENTACIÓN
│   ├── 📁 theme/                     # Tema de la app
│   │   ├── Color.kt
│   │   ├── Theme.kt
│   │   └── Type.kt
│   │
│   ├── 📁 navigation/                # Navegación
│   │   ├── NavGraph.kt
│   │   └── Screen.kt
│   │
│   ├── 📁 components/                # Componentes reutilizables
│   │   ├── ReciclaButton.kt
│   │   ├── ReciclaTextField.kt
│   │   ├── LoadingDialog.kt
│   │   └── ErrorMessage.kt
│   │
│   ├── 📁 screens/                   # Pantallas
│   │   ├── 📁 auth/                  # Autenticación
│   │   │   ├── LoginScreen.kt
│   │   │   ├── LoginViewModel.kt
│   │   │   ├── RegisterScreen.kt
│   │   │   └── RegisterViewModel.kt
│   │   │
│   │   ├── 📁 home/                  # Inicio
│   │   │   ├── HomeScreen.kt
│   │   │   └── HomeViewModel.kt
│   │   │
│   │   ├── 📁 perfil/                # Perfil de usuario
│   │   │   ├── PerfilScreen.kt
│   │   │   └── PerfilViewModel.kt
│   │   │
│   │   ├── 📁 entrega/               # Registrar entrega
│   │   │   ├── EntregaScreen.kt
│   │   │   └── EntregaViewModel.kt
│   │   │
│   │   ├── 📁 recompensas/           # Lista de recompensas
│   │   │   ├── RecompensasScreen.kt
│   │   │   └── RecompensasViewModel.kt
│   │   │
│   │   └── 📁 historial/             # Historial
│   │       ├── HistorialScreen.kt
│   │       └── HistorialViewModel.kt
│   │
│   └── 📁 utils/                     # Utilidades de UI
│       ├── ImagePicker.kt
│       └── PermissionHandler.kt
│
├── 📁 util/                          # Utilidades generales
│   ├── Constants.kt
│   ├── Resource.kt                   # Clase para manejar estados
│   └── Extensions.kt
│
├── ReciclaApp.kt                     # Clase Application
└── MainActivity.kt                   # Activity principal

```

Explicación para estudiantes:

    data/: "El almacén" - Todo lo relacionado con guardar/cargar datos
    di/: "La fábrica" - Crea objetos automáticamente
    ui/: "La cara de la app" - Todo lo que el usuario ve
    util/: "La caja de herramientas" - Funciones útiles que usarás en varios lugares

PARTE 5: MODELOS DE DATOS (Data Classes)
5.1 Modelo de Usuario

📁 data/model/Usuario.kt

```kotlin
package mx.edu.utng.reciclaDH.data.model

import com.google.firebase.firestore.DocumentId
import com.google.firebase.firestore.ServerTimestamp
import java.util.Date

/**
 * Modelo que representa un usuario de la aplicación.
 * 
 * @property id ID único del usuario (generado por Firebase Auth)
 * @property email Correo electrónico
 * @property nombre Nombre completo
 * @property telefono Teléfono de contacto
 * @property direccion Dirección del usuario
 * @property puntos Puntos acumulados por reciclaje
 * @property rol Rol del usuario: "ciudadano", "operador", "admin"
 * @property activo Si el usuario está activo en el sistema
 * @property fechaRegistro Fecha de creación de la cuenta
 * @property fotoPerfilUrl URL de la foto de perfil (opcional)
 */
data class Usuario(
    @DocumentId
    val id: String = "",
    val email: String = "",
    val nombre: String = "",
    val telefono: String = "",
    val direccion: String = "",
    val puntos: Int = 0,
    val rol: String = "ciudadano", // "ciudadano", "operador", "admin"
    val activo: Boolean = true,
    @ServerTimestamp
    val fechaRegistro: Date? = null,
    val fotoPerfilUrl: String? = null
) {
    /**
     * Valida si el usuario tiene suficientes puntos para un canje.
     */
    fun tienePuntosSuficientes(puntosRequeridos: Int): Boolean {
        return puntos >= puntosRequeridos
    }
    
    /**
     * Verifica si el usuario es administrador o operador.
     */
    fun esPersonalAutorizado(): Boolean {
        return rol in listOf("operador", "admin")
    }
    
    companion object {
        // Colección en Firestore
        const val COLLECTION_NAME = "users"
        
        // Roles disponibles
        const val ROL_CIUDADANO = "ciudadano"
        const val ROL_OPERADOR = "operador"
        const val ROL_ADMIN = "admin"
    }
}
```

5.2 Modelo de Entrega

📁 data/model/Entrega.kt

```kotlin
package mx.edu.utng.reciclaDH.data.model

import com.google.firebase.firestore.DocumentId
import com.google.firebase.firestore.GeoPoint
import com.google.firebase.firestore.ServerTimestamp
import java.util.Date

/**
 * Modelo que representa una entrega de material reciclable.
 * 
 * Ejemplo: Doña María lleva 3 kg de PET → se crea esta entrega
 */
data class Entrega(
    @DocumentId
    val id: String = "",
    val usuarioId: String = "",              // ID del usuario que entrega
    val usuarioNombre: String = "",          // Nombre (para mostrar sin consultar usuario)
    val tipoMaterial: TipoMaterial = TipoMaterial.PET,
    val pesoKg: Double = 0.0,
    val puntosGenerados: Int = 0,
    val fotoUrl: String? = null,             // URL de la foto en Storage
    val estado: EstadoEntrega = EstadoEntrega.PENDIENTE,
    val ubicacion: GeoPoint? = null,         // Ubicación GPS (opcional)
    val comentarios: String = "",
    @ServerTimestamp
    val fechaEntrega: Date? = null,
    val validadoPor: String? = null,         // ID del operador que validó
    val fechaValidacion: Date? = null,
    val motivoRechazo: String? = null        // Si fue rechazada, por qué
) {
    /**
     * Calcula puntos basados en tipo de material y peso.
     */
    fun calcularPuntos(): Int {
        return (pesoKg * tipoMaterial.puntosPorKg).toInt()
    }
    
    companion object {
        const val COLLECTION_NAME = "deliveries"
    }
}

/**
 * Tipos de material reciclable con sus puntos.
 */
enum class TipoMaterial(val display: String, val puntosPorKg: Double) {
    PET("PET (Botellas plásticas)", 10.0),
    PLASTICO("Plástico general", 8.0),
    VIDRIO("Vidrio", 5.0),
    PAPEL("Papel y cartón", 3.0),
    METAL("Metal (latas, aluminio)", 15.0),
    ELECTRONICO("Electrónico", 20

Continuar

.0),
ORGANICO("Orgánico (compostable)", 2.0);

/**
 * Obtiene el icono de emoji para mostrar en UI.
 */
fun getIcono(): String {
    return when (this) {
        PET -> "♻️"
        PLASTICO -> "🥤"
        VIDRIO -> "🍾"
        PAPEL -> "📄"
        METAL -> "🥫"
        ELECTRONICO -> "📱"
        ORGANICO -> "🌱"
    }
}

}

/**

    Estados posibles de una entrega. */ enum class EstadoEntrega(val display: String) { PENDIENTE("Pendiente de validación"), APROBADA("Aprobada"), RECHAZADA("Rechazada"); fun getColor(): androidx.compose.ui.graphics.Color { return when (this) { PENDIENTE -> androidx.compose.ui.graphics.Color(0xFFFFA726) // Naranja APROBADA -> androidx.compose.ui.graphics.Color(0xFF66BB6A) // Verde RECHAZADA -> androidx.compose.ui.graphics.Color(0xFFEF5350) // Rojo } } }


```
---

### 5.3 Modelo de Recompensa

📁 **data/model/Recompensa.kt**
```kotlin
package mx.edu.utng.reciclaDH.data.model

import com.google.firebase.firestore.DocumentId
import com.google.firebase.firestore.ServerTimestamp
import java.util.Date

/**
 * Modelo que representa una recompensa disponible para canje.
 * 
 * Ejemplo: "Beca escolar de $500" que cuesta 500 puntos
 */
data class Recompensa(
    @DocumentId
    val id: String = "",
    val titulo: String = "",                 // "Beca Escolar"
    val descripcion: String = "",            // Descripción detallada
    val categoria: CategoriaRecompensa = CategoriaRecompensa.BECA,
    val costoEnPuntos: Int = 0,
    val valorMonetario: Double = 0.0,        // Valor real en pesos
    val imagenUrl: String? = null,
    val cantidadDisponible: Int = 0,         // -1 = ilimitado
    val activa: Boolean = true,
    val requisitos: List<String> = emptyList(), // Ej: "Ser estudiante", "Mayor de edad"
    @ServerTimestamp
    val fechaCreacion: Date? = null,
    val creadoPor: String = "",              // ID del admin que la creó
    val vigenciaInicio: Date? = null,
    val vigenciaFin: Date? = null
) {
    /**
     * Verifica si la recompensa está disponible.
     */
    fun estaDisponible(): Boolean {
        val hayStock = cantidadDisponible == -1 || cantidadDisponible > 0
        val estaActiva = activa
        val estaVigente = if (vigenciaFin != null) {
            Date().before(vigenciaFin)
        } else true
        
        return hayStock && estaActiva && estaVigente
    }
    
    /**
     * Reduce la cantidad disponible (después de un canje).
     */
    fun reducirStock(): Recompensa {
        return if (cantidadDisponible > 0) {
            this.copy(cantidadDisponible = cantidadDisponible - 1)
        } else {
            this
        }
    }
    
    companion object {
        const val COLLECTION_NAME = "rewards"
        const val STOCK_ILIMITADO = -1
    }
}

/**
 * Categorías de recompensas.
 */
enum class CategoriaRecompensa(val display: String, val icono: String) {
    BECA("Becas Educativas", "🎓"),
    APOYO_ECONOMICO("Apoyos Económicos", "💰"),
    DESCUENTO("Descuentos Municipales", "🏛️"),
    SERVICIO("Servicios Públicos", "🚰"),
    CULTURA("Eventos Culturales", "🎭"),
    DEPORTE("Deportes y Recreación", "⚽"),
    SALUD("Servicios de Salud", "🏥");
}
```

---

### 5.4 Modelo de Canje

📁 **data/model/Canje.kt**
```kotlin
package mx.edu.utng.reciclaDH.data.model

import com.google.firebase.firestore.DocumentId
import com.google.firebase.firestore.ServerTimestamp
import java.util.Date

/**
 * Modelo que representa un canje de puntos por recompensa.
 * 
 * Flujo: Usuario solicita canje → Admin lo revisa → Aprueba/Rechaza → Entrega
 */
data class Canje(
    @DocumentId
    val id: String = "",
    val usuarioId: String = "",
    val usuarioNombre: String = "",
    val recompensaId: String = "",
    val recompensaTitulo: String = "",       // Para mostrar sin consultar
    val puntosUtilizados: Int = 0,
    val estado: EstadoCanje = EstadoCanje.SOLICITADO,
    @ServerTimestamp
    val fechaSolicitud: Date? = null,
    val revisadoPor: String? = null,         // ID del admin
    val fechaRevision: Date? = null,
    val motivoRechazo: String? = null,
    val fechaEntrega: Date? = null,
    val comentariosUsuario: String = "",     // Comentarios del ciudadano
    val comentariosAdmin: String = "",       // Comentarios del administrador
    val comprobanteUrl: String? = null       // URL de comprobante de entrega
) {
    /**
     * Verifica si el canje está en un estado final.
     */
    fun estaFinalizado(): Boolean {
        return estado in listOf(
            EstadoCanje.ENTREGADO,
            EstadoCanje.RECHAZADO,
            EstadoCanje.CANCELADO
        )
    }
    
    /**
     * Verifica si se pueden devolver los puntos al usuario.
     */
    fun permiteDevolucionPuntos(): Boolean {
        return estado in listOf(
            EstadoCanje.RECHAZADO,
            EstadoCanje.CANCELADO
        )
    }
    
    companion object {
        const val COLLECTION_NAME = "redemptions"
    }
}

/**
 * Estados del proceso de canje.
 */
enum class EstadoCanje(val display: String) {
    SOLICITADO("Solicitado - En revisión"),
    APROBADO("Aprobado - Pendiente de entrega"),
    EN_PROCESO("En proceso de entrega"),
    ENTREGADO("Entregado"),
    RECHAZADO("Rechazado"),
    CANCELADO("Cancelado por usuario");
    
    fun getColor(): androidx.compose.ui.graphics.Color {
        return when (this) {
            SOLICITADO -> androidx.compose.ui.graphics.Color(0xFFFFA726) // Naranja
            APROBADO -> androidx.compose.ui.graphics.Color(0xFF42A5F5) // Azul
            EN_PROCESO -> androidx.compose.ui.graphics.Color(0xFF66BB6A) // Verde claro
            ENTREGADO -> androidx.compose.ui.graphics.Color(0xFF4CAF50) // Verde
            RECHAZADO, CANCELADO -> androidx.compose.ui.graphics.Color(0xFFEF5350) // Rojo
        }
    }
    
    fun getIcono(): String {
        return when (this) {
            SOLICITADO -> "⏳"
            APROBADO -> "✅"
            EN_PROCESO -> "🚚"
            ENTREGADO -> "🎉"
            RECHAZADO -> "❌"
            CANCELADO -> "🚫"
        }
    }
}
```

---

## **PARTE 6: UTILIDADES (Resource y Extensions)**

### 6.1 Clase Resource para manejo de estados

📁 **util/Resource.kt**
```kotlin
package mx.edu.utng.reciclaDH.util

/**
 * Clase sellada que representa el estado de una operación.
 * 
 * ¿Por qué usar esto?
 * Cuando cargamos datos de Firebase, pueden pasar 3 cosas:
 * 1. Está cargando (Loading)
 * 2. Tuvo éxito (Success)
 * 3. Falló (Error)
 * 
 * Esta clase nos permite manejar los 3 casos de forma elegante.
 * 
 * Ejemplo de uso en ViewModel:
 * ```
 * private val _usuario = MutableStateFlow<Resource<Usuario>>(Resource.Loading())
 * val usuario: StateFlow<Resource<Usuario>> = _usuario.asStateFlow()
 * 
 * fun cargarUsuario() {
 *     viewModelScope.launch {
 *         _usuario.value = Resource.Loading()
 *         try {
 *             val data = repository.obtenerUsuario()
 *             _usuario.value = Resource.Success(data)
 *         } catch (e: Exception) {
 *             _usuario.value = Resource.Error(e.message ?: "Error desconocido")
 *         }
 *     }
 * }
 * ```
 * 
 * Ejemplo de uso en Composable:
 * ```
 * val usuarioState by viewModel.usuario.collectAsState()
 * 
 * when (usuarioState) {
 *     is Resource.Loading -> CircularProgressIndicator()
 *     is Resource.Success -> Text("Hola ${usuarioState.data?.nombre}")
 *     is Resource.Error -> Text("Error: ${usuarioState.message}")
 * }
 * ```
 */
sealed class Resource<T>(
    val data: T? = null,
    val message: String? = null
) {
    /**
     * Estado de carga - muestra indicador de progreso.
     */
    class Loading<T>(data: T? = null) : Resource<T>(data)
    
    /**
     * Estado exitoso - muestra los datos.
     */
    class Success<T>(data: T) : Resource<T>(data)
    
    /**
     * Estado de error - muestra mensaje de error.
     */
    class Error<T>(message: String, data: T? = null) : Resource<T>(data, message)
}
```

---

### 6.2 Constantes de la aplicación

📁 **util/Constants.kt**
```kotlin
package mx.edu.utng.reciclaDH.util

/**
 * Constantes usadas en toda la aplicación.
 * 
 * Ventaja: Si necesitas cambiar un valor, solo lo cambias aquí.
 */
object Constants {
    
    // Firebase Collections
    const val USERS_COLLECTION = "users"
    const val DELIVERIES_COLLECTION = "deliveries"
    const val REWARDS_COLLECTION = "rewards"
    const val REDEMPTIONS_COLLECTION = "redemptions"
    
    // Firebase Storage
    const val STORAGE_DELIVERIES_PATH = "deliveries"
    const val STORAGE_PROFILES_PATH = "profiles"
    const val STORAGE_REWARDS_PATH = "rewards"
    const val STORAGE_RECEIPTS_PATH = "receipts"
    
    // Puntos y conversiones
    const val PUNTOS_POR_PESO_DEFAULT = 10 // 10 puntos por kg
    const val MIN_PESO_KG = 0.1
    const val MAX_PESO_KG = 1000.0
    
    // Límites de la app
    const val MAX_IMAGE_SIZE_MB = 5
    const val MAX_IMAGE_DIMENSION = 1920
    
    // Roles de usuario
    const val ROL_CIUDADANO = "ciudadano"
    const val ROL_OPERADOR = "operador"
    const val ROL_ADMIN = "admin"
    
    // Preferencias / SharedPreferences keys (si usáramos)
    const val PREF_USER_ID = "user_id"
    const val PREF_IS_FIRST_TIME = "is_first_time"
    
    // Navigation
    const val NAV_ARG_USER_ID = "userId"
    const val NAV_ARG_DELIVERY_ID = "deliveryId"
    const val NAV_ARG_REWARD_ID = "rewardId"
    
    // Validaciones
    const val MIN_PASSWORD_LENGTH = 6
    const val PHONE_LENGTH = 10
    
    // Mensajes comunes
    const val ERROR_NETWORK = "Error de conexión. Verifica tu internet."
    const val ERROR_UNKNOWN = "Ocurrió un error inesperado."
    const val SUCCESS_SAVE = "Guardado exitosamente."
    const val SUCCESS_DELETE = "Eliminado exitosamente."
    
    // Tiempos (milisegundos)
    const val SPLASH_DELAY = 2000L
    const val DEBOUNCE_DELAY = 500L
    const val TIMEOUT_NETWORK = 30000L
}
```

---

### 6.3 Extensiones útiles

📁 **util/Extensions.kt**
```kotlin
package mx.edu.utng.reciclaDH.util

import android.content.Context
import android.widget.Toast
import java.text.SimpleDateFormat
import java.util.*

/**
 * Extensiones de Kotlin para simplificar código común.
 * 
 * Las extensiones nos permiten "agregar métodos" a clases existentes.
 */

// ============ EXTENSIONES PARA STRING ============

/**
 * Valida si un email tiene formato válido.
 * Ejemplo: "juan@gmail.com".isValidEmail() → true
 */
fun String.isValidEmail(): Boolean {
    return android.util.Patterns.EMAIL_ADDRESS.matcher(this).matches()
}

/**
 * Valida si un teléfono tiene formato válido (10 dígitos).
 */
fun String.isValidPhone(): Boolean {
    return this.matches(Regex("^[0-9]{10}$"))
}

/**
 * Capitaliza la primera letra de cada palabra.
 * Ejemplo: "juan pérez".toTitleCase() → "Juan Pérez"
 */
fun String.toTitleCase(): String {
    return this.split(" ").joinToString(" ") { word ->
        word.replaceFirstChar { 
            if (it.isLowerCase()) it.titlecase(Locale.getDefault()) else it.toString() 
        }
    }
}

// ============ EXTENSIONES PARA DATE ============

/**
 * Formatea fecha a string legible.
 * Ejemplo: Date().toFormattedString() → "27 Oct 2025"
 */
fun Date.toFormattedString(pattern: String = "dd MMM yyyy"): String {
    val formatter = SimpleDateFormat(pattern, Locale("es", "MX"))
    return formatter.format(this)
}

/**
 * Formatea fecha y hora.
 * Ejemplo: Date().toFormattedDateTime() → "27 Oct 2025, 10:30 AM"
 */
fun Date.toFormattedDateTime(): String {
    val formatter = SimpleDateFormat("dd MMM yyyy, hh:mm a", Locale("es", "MX"))
    return formatter.format(this)
}

/**
 * Obtiene tiempo relativo ("hace 2 horas").
 */
fun Date.toRelativeTime(): String {
    val now = Date()
    val diff = now.time - this.time
    
    val seconds = diff / 1000
    val minutes = seconds / 60
    val hours = minutes / 60
    val days = hours / 24
    val weeks = days / 7
    
    return when {
        seconds < 60 -> "Hace un momento"
        minutes < 60 -> "Hace ${minutes}m"
        hours < 24 -> "Hace ${hours}h"
        days < 7 -> "Hace ${days}d"
        weeks < 4 -> "Hace ${weeks} semanas"
        else -> this.toFormattedString()
    }
}

// ============ EXTENSIONES PARA DOUBLE ============

/**
 * Formatea número a moneda mexicana.
 * Ejemplo: 1500.0.toMoney() → "$1,500.00"
 */
fun Double.toMoney(): String {
    return String.format(Locale("es", "MX"), "$%,.2f", this)
}

/**
 * Redondea a 2 decimales.
 */
fun Double.roundTo2Decimals(): Double {
    return String.format(Locale.US, "%.2f", this).toDouble()
}

// ============ EXTENSIONES PARA INT ============

/**
 * Formatea número de puntos.
 * Ejemplo: 1500.formatPoints() → "1,500 pts"
 */
fun Int.formatPoints(): String {
    return String.format(Locale("es", "MX"), "%,d pts", this)
}

// ============ EXTENSIONES PARA CONTEXT ============

/**
 * Muestra Toast de forma más simple.
 * Ejemplo: context.showToast("Guardado exitoso")
 */
fun Context.showToast(message: String, duration: Int = Toast.LENGTH_SHORT) {
    Toast.makeText(this, message, duration).show()
}

/**
 * Muestra Toast largo.
 */
fun Context.showLongToast(message: String) {
    Toast.makeText(this, message, Toast.LENGTH_LONG).show()
}

// ============ EXTENSIONES PARA COLLECTIONS ============

/**
 * Suma segura de puntos (evita nulls).
 */
fun List<Int?>.sumOfPoints(): Int {
    return this.filterNotNull().sum()
}

/**
 * Agrupa y suma puntos por tipo.
 */
fun <T> List<T>.sumByInt(selector: (T) -> Int): Int {
    return this.map(selector).sum()
}
```

---

## **PARTE 7: REPOSITORIOS (Acceso a Firebase)**

### 7.1 AuthRepository (Autenticación)

📁 **data/repository/AuthRepository.kt**
```kotlin
package mx.edu.utng.reciclaDH.data.repository

import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.auth.FirebaseUser
import com.google.firebase.auth.GoogleAuthProvider
import com.google.firebase.firestore.FirebaseFirestore
import kotlinx.coroutines.tasks.await
import mx.edu.utng.reciclaDH.data.model.Usuario
import mx.edu.utng.reciclaDH.util.Resource
import javax.inject.Inject
import javax.inject.Singleton

/**
 * Repositorio que maneja la autenticación de usuarios.
 * 
 * Este repositorio es como "el portero" de la app:
 * - Deja entrar a usuarios registrados (login)
 * - Registra nuevos usuarios (registro)
 * - Verifica credenciales
 * - Maneja cierre de sesión
 * 
 * @Inject: Hilt automáticamente pasa FirebaseAuth y Firestore
 * @Singleton: Solo se crea una instancia de este repositorio
 */
@Singleton
class AuthRepository @Inject constructor(
    private val auth: FirebaseAuth,
    private val firestore: FirebaseFirestore
) {
    
    /**
     * Usuario actual autenticado (null si no hay sesión).
     */
    val currentUser: FirebaseUser?
        get() = auth.currentUser
    
    /**
     * ID del usuario actual.
     */
    val currentUserId: String?
        get() = auth.currentUser?.uid
    
    /**
     * Verifica si hay un usuario autenticado.
     */
    fun isUserAuthenticated(): Boolean {
        return auth.currentUser != null
    }
    
    /**
     * Registra un nuevo usuario con email y contraseña.
     * 
     * Flujo:
     * 1. Crear cuenta en Firebase Auth
     * 2. Crear documento de usuario en Firestore
     * 3. Retornar resultado
     * 
     * @param email Correo electrónico
     * @param password Contraseña (mínimo 6 caracteres)
     * @param nombre Nombre completo
     * @param telefono Teléfono de contacto
     * 
     * @return Resource<Usuario> con el usuario creado o error
     */
    suspend fun registrarUsuario(
        email: String,
        password: String,
        nombre: String,
        telefono: String
    ): Resource<Usuario> {
        return try {
            // Paso 1: Crear usuario en Firebase Auth
            val result = auth.createUserWithEmailAndPassword(email, password).await()
            val firebaseUser = result.user
                ?: return Resource.Error("Error al crear usuario")
            
            // Paso 2: Crear perfil de usuario en Firestore
            val usuario = Usuario(
                id = firebaseUser.uid,
                email = email,
                nombre = nombre,
                telefono = telefono,
                puntos = 0,
                rol = Usuario.ROL_CIUDADANO,
                activo = true
            )
            
            // Guardar en Firestore
            firestore.collection(Usuario.COLLECTION_NAME)
                .document(firebaseUser.uid)
                .set(usuario)
                .await()
            
            Resource.Success(usuario)
            
        } catch (e: Exception) {
            Resource.Error(
                message = when {
                    e.message?.contains("email address is already") == true ->
                        "Este correo ya está registrado"
                    e.message?.contains("network") == true ->
                        "Error de conexión. Verifica tu internet"
                    e.message?.contains("password") == true ->
                        "La contraseña debe tener al menos 6 caracteres"
                    else -> e.message ?: "Error desconocido al registrar"
                }
            )
        }
    }
    
    /**
     * Inicia sesión con email y contraseña.
     * 
     * @param email Correo electrónico
     * @param password Contraseña
     * 
     * @return Resource<Usuario> con datos del usuario o error
     */
    suspend fun iniciarSesion(
        email: String,
        password: String
    ): Resource<Usuario> {
        return try {
            // Autenticar con Firebase Auth
            val result = auth.signInWithEmailAndPassword(email, password).await()
            val firebaseUser = result.user
                ?: return Resource.Error("Error al iniciar sesión")
            
            // Obtener datos del usuario desde Firestore
            val usuarioDoc = firestore.collection(Usuario.COLLECTION_NAME)
                .document(firebaseUser.uid)
                .get()
                .await()
            
            val usuario = usuarioDoc.toObject(Usuario::class.java)
                ?: return Resource.Error("Usuario no encontrado en la base de datos")
            
            // Verificar si el usuario está activo
            if (!usuario.activo) {
                auth.signOut() // Cerrar sesión inmediatamente
                return Resource.Error("Tu cuenta ha sido desactivada. Contacta al administrador")
            }
            
            Resource.Success(usuario)
            
        } catch (e: Exception) {
            Resource.Error(
                message = when {
                    e.message?.contains("no user record") == true ||
                    e.message?.contains("password is invalid") == true ->
                        "Correo o contraseña incorrectos"
                    e.message?.contains("network") == true ->
                        "Error de conexión. Verifica tu internet"
                    else -> e.message ?: "Error desconocido al iniciar sesión"
                }
            )
        }
    }
    
    /**
     * Inicia sesión con Google.
     * 
     * @param idToken Token de Google Sign-In
     * 
     * @return Resource<Usuario> con datos del usuario
     */
    suspend fun iniciarSesionConGoogle(idToken: String): Resource<Usuario> {
        return try {
            // Crear credencial de Google
            val credential = GoogleAuthProvider.getCredential(idToken, null)
            
            // Autenticar con Firebase
            val result = auth.signInWithCredential(credential).await()
            val firebaseUser = result.user
                ?: return Resource.Error("Error al iniciar sesión con Google")
            
            // Verificar si el usuario ya existe en Firestore
            val usuarioDoc = firestore.collection(Usuario.COLLECTION_NAME)
                .document(firebaseUser.uid)
                .get()
                .await()
            
            val usuario = if (usuarioDoc.exists()) {
                // Usuario existente
                usuarioDoc.toObject(Usuario::class.java)!!
            } else {
                // Nuevo usuario - crear perfil
                val nuevoUsuario = Usuario(
                    id = firebaseUser.uid,
                    email = firebaseUser.email ?: "",
                    nombre = firebaseUser.displayName ?: "",
                    telefono = firebaseUser.phoneNumber ?: "",
                    puntos = 0,
                    rol = Usuario.ROL_CIUDADANO,
                    activo = true,
                    fotoPerfilUrl = firebaseUser.photoUrl?.toString()
                )
                
                firestore.collection(Usuario.COLLECTION_NAME)
                    .document(firebaseUser.uid)
                    .set(nuevoUsuario)
                    .await()
                
                nuevoUsuario
            }
            
            Resource.Success(usuario)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al iniciar sesión con Google")
        }
    }
    
    /**
     * Cierra la sesión del usuario actual.
     */
    fun cerrarSesion() {
        auth.signOut()
    }
    
    /**
     * Envía email para restablecer contraseña.
     * 
     * @param email Correo electrónico
     * 
     * @return Resource<Unit> éxito o error
     */
    suspend fun enviarEmailRecuperacion(email: String): Resource<Unit> {
        return try {
            auth.sendPasswordResetEmail(email).await()
            Resource.Success(Unit)
        } catch (e: Exception) {
            Resource.Error(
                message = when {
                    e.message?.contains("no user record") == true ->
                        "No existe una cuenta con este correo"
                    else -> e.message ?: "Error al enviar email de recuperación"
                }
            )
        }
    }
    
    /**
     * Actualiza la contraseña del usuario actual.
     * 
     * @param newPassword Nueva contraseña
     */
    suspend fun actualizarPassword(newPassword: String): Resource<Unit> {
        return try {
            val user = auth.currentUser
                ?: return Resource.Error("No hay usuario autenticado")
            
            user.updatePassword(newPassword).await()
            Resource.Success(Unit)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al actualizar contraseña")
        }
    }
}
```

---

### 7.2 UsuarioRepository

📁 **data/repository/UsuarioRepository.kt**
```kotlin
package mx.edu.utng.reciclaDH.data.repository

import com.google.firebase.firestore.FirebaseFirestore
import com.google.firebase.firestore.ListenerRegistration
import com.google.firebase.storage.FirebaseStorage
import kotlinx.coroutines.channels.awaitClose
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.callbackFlow
import kotlinx.coroutines.tasks.await
import mx.edu.utng.reciclaDH.data.model.Usuario
import mx.edu.utng.reciclaDH.util.Resource
import java.io.File
import javax.inject.Inject
import javax.inject.Singleton

/**
 * Repositorio para operaciones relacionadas con usuarios.
 * 
 * Responsabilidades:
 * - Obtener datos de usuario
 * - Actualizar perfil
 * - Subir foto de perfil
 * - Administrar puntos
 */
@Singleton
class UsuarioRepository @Inject constructor(
    private val firestore: FirebaseFirestore,
    private val storage: FirebaseStorage
) {
    
    /**
     * Obtiene un usuario por su ID.
     * 
     * @param usuarioId ID del usuario
     * @return Resource<Usuario>
     */
    suspend fun obtenerUsuario(usuarioId: String): Resource<Usuario> {
        return try {
            val doc = firestore.collection(Usuario.COLLECTION_NAME)
                .document(usuarioId)
                .get()
                .await()
            
            val usuario = doc.toObject(Usuario::class.java)
                ?: return Resource.Error("Usuario no encontrado")
            
            Resource.Success(usuario)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al obtener usuario")
        }
    }
    
    /**
     * Obtiene un Flow que emite cambios en tiempo real del usuario.
     * 
     * Esto es útil para que la UI se actualice automáticamente
     * cuando cambian los puntos u otros datos del usuario.
     * 
     * Ejemplo de uso en ViewModel:
     * ```
     * val usuario: StateFlow<Resource<Usuario>> = repository
     *     .obtenerUsuarioFlow(userId)
     *     .stateIn(viewModelScope, SharingStarted.WhileSubscribed(), Resource.Loading())
     * ```
     */
    fun obtenerUsuarioFlow(usuarioId: String): Flow<Resource<Usuario>> = callbackFlow {
        // Enviar estado inicial
        trySend(Resource.Loading())
        
        // Listener para cambios en tiempo real
        val listener: ListenerRegistration = firestore
            .collection(Usuario.COLLECTION_NAME)
            .document(usuarioId)
            .addSnapshotListener { snapshot, error ->
                if (error != null) {
                    trySend(Resource.Error(error.message ?: "Error desconocido"))
                    return@addSnapshotListener
                }
                
                val usuario = snapshot?.toObject(Usuario::class.java)
                if (usuario != null) {
                    trySend(Resource.Success(usuario))
                } else {
                    trySend(Resource.Error("Usuario no encontrado"))
                }
            }
        
        // Cancelar listener cuando se cancele el Flow
        awaitClose { listener.remove() }
    }
    
    /**
     * Actualiza el perfil del usuario.
     * 
     * @param usuarioId ID del usuario
     * @param updates Mapa con los campos a actualizar
     * 
     * Ejemplo:
     * ```
     * repository.actualizarPerfil(
     *     userId,
     *     mapOf(
     *         "nombre" to "Juan Pérez",
     *         "telefono" to "4771234567"
     *     )
     * )
     * ```
     */
    suspend fun actualizarPerfil(
        usuarioId: String,
        updates: Map<String, Any>
    ): Resource<Unit> {
        return try {
            firestore.collection(Usuario.COLLECTION_NAME

Continuar

)
.document(usuarioId)
.update(updates)
.await()

        Resource.Success(Unit)
        
    } catch (e: Exception) {
        Resource.Error(e.message ?: "Error al actualizar perfil")
    }
}

/**
 * Sube una foto de perfil al Storage y actualiza la URL en Firestore.
 * 
 * Flujo:
 * 1. Subir imagen a Firebase Storage
 * 2. Obtener URL de descarga
 * 3. Actualizar campo fotoPerfilUrl en Firestore
 * 
 * @param usuarioId ID del usuario
 * @param imageFile Archivo de imagen
 * 
 * @return Resource<String> con la URL de la imagen o error
 */
suspend fun subirFotoPerfil(
    usuarioId: String,
    imageFile: File
): Resource<String> {
    return try {
        // Referencia en Storage: profiles/{usuarioId}/perfil.jpg
        val storageRef = storage.reference
            .child("profiles")
            .child(usuarioId)
            .child("perfil.jpg")
        
        // Subir archivo
        storageRef.putFile(android.net.Uri.fromFile(imageFile)).await()
        
        // Obtener URL de descarga
        val downloadUrl = storageRef.downloadUrl.await().toString()
        
        // Actualizar Firestore
        firestore.collection(Usuario.COLLECTION_NAME)
            .document(usuarioId)
            .update("fotoPerfilUrl", downloadUrl)
            .await()
        
        Resource.Success(downloadUrl)
        
    } catch (e: Exception) {
        Resource.Error(e.message ?: "Error al subir foto de perfil")
    }
}

/**
 * Suma puntos al usuario.
 * 
 * Usa FieldValue.increment() para evitar condiciones de carrera.
 * 
 * @param usuarioId ID del usuario
 * @param puntos Cantidad de puntos a sumar
 */
suspend fun sumarPuntos(
    usuarioId: String,
    puntos: Int
): Resource<Unit> {
    return try {
        firestore.collection(Usuario.COLLECTION_NAME)
            .document(usuarioId)
            .update("puntos", com.google.firebase.firestore.FieldValue.increment(puntos.toLong()))
            .await()
        
        Resource.Success(Unit)
        
    } catch (e: Exception) {
        Resource.Error(e.message ?: "Error al sumar puntos")
    }
}

/**
 * Resta puntos al usuario (para canjes).
 * 
 * IMPORTANTE: No verifica si el usuario tiene suficientes puntos.
 * Esta verificación debe hacerse ANTES de llamar este método.
 * 
 * @param usuarioId ID del usuario
 * @param puntos Cantidad de puntos a restar
 */
suspend fun restarPuntos(
    usuarioId: String,
    puntos: Int
): Resource<Unit> {
    return try {
        firestore.collection(Usuario.COLLECTION_NAME)
            .document(usuarioId)
            .update("puntos", com.google.firebase.firestore.FieldValue.increment(-puntos.toLong()))
            .await()
        
        Resource.Success(Unit)
        
    } catch (e: Exception) {
        Resource.Error(e.message ?: "Error al restar puntos")
    }
}

/**
 * Obtiene el ranking de usuarios por puntos (top 10).
 * 
 * Útil para mostrar una tabla de líderes.
 */
suspend fun obtenerRankingUsuarios(limite: Int = 10): Resource<List<Usuario>> {
    return try {
        val snapshot = firestore.collection(Usuario.COLLECTION_NAME)
            .whereEqualTo("rol", Usuario.ROL_CIUDADANO) // Solo ciudadanos
            .whereEqualTo("activo", true)
            .orderBy("puntos", com.google.firebase.firestore.Query.Direction.DESCENDING)
            .limit(limite.toLong())
            .get()
            .await()
        
        val usuarios = snapshot.toObjects(Usuario::class.java)
        Resource.Success(usuarios)
        
    } catch (e: Exception) {
        Resource.Error(e.message ?: "Error al obtener ranking")
    }
}

/**
 * Busca usuarios por nombre (para admins).
 * 
 * Nota: Firestore no soporta búsquedas tipo LIKE nativas.
 * Esta implementación busca coincidencias exactas al inicio del nombre.
 * Para búsquedas más avanzadas, considera usar Algolia o ElasticSearch.
 */
suspend fun buscarUsuarios(query: String): Resource<List<Usuario>> {
    return try {
        val snapshot = firestore.collection(Usuario.COLLECTION_NAME)
            .orderBy("nombre")
            .startAt(query)
            .endAt(query + "\uf8ff")
            .limit(20)
            .get()
            .await()
        
        val usuarios = snapshot.toObjects(Usuario::class.java)
        Resource.Success(usuarios)
        
    } catch (e: Exception) {
        Resource.Error(e.message ?: "Error al buscar usuarios")
    }
}

}

```

---

### 7.3 EntregaRepository

📁 **data/repository/EntregaRepository.kt**
```kotlin
package mx.edu.utng.reciclaDH.data.repository

import android.net.Uri
import com.google.firebase.firestore.FirebaseFirestore
import com.google.firebase.firestore.Query
import com.google.firebase.storage.FirebaseStorage
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.callbackFlow
import kotlinx.coroutines.tasks.await
import mx.edu.utng.reciclaDH.data.model.Entrega
import mx.edu.utng.reciclaDH.data.model.EstadoEntrega
import mx.edu.utng.reciclaDH.util.Resource
import java.util.*
import javax.inject.Inject
import javax.inject.Singleton

/**
 * Repositorio para gestionar entregas de material reciclable.
 * 
 * Flujo completo de una entrega:
 * 1. Usuario crea entrega (estado: PENDIENTE) → crearEntrega()
 * 2. Operador la revisa y valida → validarEntrega()
 * 3. Si es aprobada, se suman puntos al usuario
 * 4. Si es rechazada, no se suman puntos
 */
@Singleton
class EntregaRepository @Inject constructor(
    private val firestore: FirebaseFirestore,
    private val storage: FirebaseStorage,
    private val usuarioRepository: UsuarioRepository
) {
    
    /**
     * Crea una nueva entrega de material reciclable.
     * 
     * Flujo:
     * 1. Subir foto a Storage (si existe)
     * 2. Calcular puntos según peso y tipo de material
     * 3. Crear documento en Firestore con estado PENDIENTE
     * 
     * @param entrega Datos de la entrega
     * @param imagenUri URI de la foto (opcional)
     * 
     * @return Resource<String> con ID de la entrega creada
     */
    suspend fun crearEntrega(
        entrega: Entrega,
        imagenUri: Uri?
    ): Resource<String> {
        return try {
            // Paso 1: Subir imagen si existe
            val fotoUrl = if (imagenUri != null) {
                subirImagenEntrega(imagenUri)
            } else null
            
            // Paso 2: Calcular puntos
            val puntosGenerados = entrega.calcularPuntos()
            
            // Paso 3: Crear documento
            val entregaConDatos = entrega.copy(
                fotoUrl = fotoUrl,
                puntosGenerados = puntosGenerados,
                estado = EstadoEntrega.PENDIENTE
            )
            
            val docRef = firestore.collection(Entrega.COLLECTION_NAME)
                .add(entregaConDatos)
                .await()
            
            Resource.Success(docRef.id)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al crear entrega")
        }
    }
    
    /**
     * Sube imagen de una entrega a Storage.
     * 
     * @param imageUri URI de la imagen
     * @return URL de descarga de la imagen
     */
    private suspend fun subirImagenEntrega(imageUri: Uri): String {
        val timestamp = System.currentTimeMillis()
        val filename = "delivery_${timestamp}.jpg"
        
        val storageRef = storage.reference
            .child("deliveries")
            .child(filename)
        
        storageRef.putFile(imageUri).await()
        return storageRef.downloadUrl.await().toString()
    }
    
    /**
     * Obtiene entregas del usuario en tiempo real.
     * 
     * @param usuarioId ID del usuario
     * @return Flow con lista de entregas ordenadas por fecha (más recientes primero)
     */
    fun obtenerEntregasUsuario(usuarioId: String): Flow<Resource<List<Entrega>>> = callbackFlow {
        trySend(Resource.Loading())
        
        val listener = firestore.collection(Entrega.COLLECTION_NAME)
            .whereEqualTo("usuarioId", usuarioId)
            .orderBy("fechaEntrega", Query.Direction.DESCENDING)
            .addSnapshotListener { snapshot, error ->
                if (error != null) {
                    trySend(Resource.Error(error.message ?: "Error desconocido"))
                    return@addSnapshotListener
                }
                
                val entregas = snapshot?.toObjects(Entrega::class.java) ?: emptyList()
                trySend(Resource.Success(entregas))
            }
        
        awaitClose { listener.remove() }
    }
    
    /**
     * Obtiene todas las entregas pendientes de validación.
     * 
     * Usado por operadores para revisar entregas.
     */
    fun obtenerEntregasPendientes(): Flow<Resource<List<Entrega>>> = callbackFlow {
        trySend(Resource.Loading())
        
        val listener = firestore.collection(Entrega.COLLECTION_NAME)
            .whereEqualTo("estado", EstadoEntrega.PENDIENTE.name)
            .orderBy("fechaEntrega", Query.Direction.ASCENDING)
            .addSnapshotListener { snapshot, error ->
                if (error != null) {
                    trySend(Resource.Error(error.message ?: "Error desconocido"))
                    return@addSnapshotListener
                }
                
                val entregas = snapshot?.toObjects(Entrega::class.java) ?: emptyList()
                trySend(Resource.Success(entregas))
            }
        
        awaitClose { listener.remove() }
    }
    
    /**
     * Obtiene una entrega específica por su ID.
     */
    suspend fun obtenerEntrega(entregaId: String): Resource<Entrega> {
        return try {
            val doc = firestore.collection(Entrega.COLLECTION_NAME)
                .document(entregaId)
                .get()
                .await()
            
            val entrega = doc.toObject(Entrega::class.java)
                ?: return Resource.Error("Entrega no encontrada")
            
            Resource.Success(entrega)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al obtener entrega")
        }
    }
    
    /**
     * Valida una entrega (aprobar o rechazar).
     * 
     * Este método debe ser llamado solo por operadores o admins.
     * 
     * Flujo si se APRUEBA:
     * 1. Actualizar estado de entrega a APROBADA
     * 2. Sumar puntos al usuario
     * 3. Registrar quién validó y cuándo
     * 
     * Flujo si se RECHAZA:
     * 1. Actualizar estado a RECHAZADA
     * 2. Registrar motivo de rechazo
     * 3. NO sumar puntos
     * 
     * @param entregaId ID de la entrega
     * @param aprobada true para aprobar, false para rechazar
     * @param operadorId ID del operador que valida
     * @param motivoRechazo Motivo si es rechazada (opcional)
     */
    suspend fun validarEntrega(
        entregaId: String,
        aprobada: Boolean,
        operadorId: String,
        motivoRechazo: String? = null
    ): Resource<Unit> {
        return try {
            // Obtener entrega actual
            val entregaDoc = firestore.collection(Entrega.COLLECTION_NAME)
                .document(entregaId)
                .get()
                .await()
            
            val entrega = entregaDoc.toObject(Entrega::class.java)
                ?: return Resource.Error("Entrega no encontrada")
            
            // Verificar que esté pendiente
            if (entrega.estado != EstadoEntrega.PENDIENTE) {
                return Resource.Error("Esta entrega ya fue validada")
            }
            
            // Preparar actualización
            val updates = mutableMapOf<String, Any>(
                "estado" to if (aprobada) EstadoEntrega.APROBADA.name else EstadoEntrega.RECHAZADA.name,
                "validadoPor" to operadorId,
                "fechaValidacion" to Date()
            )
            
            if (!aprobada && motivoRechazo != null) {
                updates["motivoRechazo"] = motivoRechazo
            }
            
            // Actualizar entrega
            firestore.collection(Entrega.COLLECTION_NAME)
                .document(entregaId)
                .update(updates)
                .await()
            
            // Si fue aprobada, sumar puntos al usuario
            if (aprobada) {
                usuarioRepository.sumarPuntos(
                    entrega.usuarioId,
                    entrega.puntosGenerados
                )
            }
            
            Resource.Success(Unit)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al validar entrega")
        }
    }
    
    /**
     * Obtiene estadísticas de entregas del usuario.
     * 
     * @return Map con estadísticas: totalEntregas, aprobadas, rechazadas, pendientes, totalPuntos
     */
    suspend fun obtenerEstadisticasUsuario(usuarioId: String): Resource<Map<String, Int>> {
        return try {
            val snapshot = firestore.collection(Entrega.COLLECTION_NAME)
                .whereEqualTo("usuarioId", usuarioId)
                .get()
                .await()
            
            val entregas = snapshot.toObjects(Entrega::class.java)
            
            val stats = mapOf(
                "totalEntregas" to entregas.size,
                "aprobadas" to entregas.count { it.estado == EstadoEntrega.APROBADA },
                "rechazadas" to entregas.count { it.estado == EstadoEntrega.RECHAZADA },
                "pendientes" to entregas.count { it.estado == EstadoEntrega.PENDIENTE },
                "totalPuntos" to entregas
                    .filter { it.estado == EstadoEntrega.APROBADA }
                    .sumOf { it.puntosGenerados }
            )
            
            Resource.Success(stats)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al obtener estadísticas")
        }
    }
    
    /**
     * Elimina una entrega (solo si está pendiente).
     * 
     * Útil si el usuario se equivocó al registrar.
     */
    suspend fun eliminarEntrega(entregaId: String, usuarioId: String): Resource<Unit> {
        return try {
            val doc = firestore.collection(Entrega.COLLECTION_NAME)
                .document(entregaId)
                .get()
                .await()
            
            val entrega = doc.toObject(Entrega::class.java)
                ?: return Resource.Error("Entrega no encontrada")
            
            // Verificar que sea del usuario
            if (entrega.usuarioId != usuarioId) {
                return Resource.Error("No tienes permiso para eliminar esta entrega")
            }
            
            // Solo se puede eliminar si está pendiente
            if (entrega.estado != EstadoEntrega.PENDIENTE) {
                return Resource.Error("Solo puedes eliminar entregas pendientes")
            }
            
            // Eliminar documento
            firestore.collection(Entrega.COLLECTION_NAME)
                .document(entregaId)
                .delete()
                .await()
            
            // Eliminar imagen si existe
            entrega.fotoUrl?.let { url ->
                try {
                    storage.getReferenceFromUrl(url).delete().await()
                } catch (e: Exception) {
                    // No es crítico si falla
                }
            }
            
            Resource.Success(Unit)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al eliminar entrega")
        }
    }
}
```

---

### 7.4 RecompensaRepository

📁 **data/repository/RecompensaRepository.kt**
```kotlin
package mx.edu.utng.reciclaDH.data.repository

import android.net.Uri
import com.google.firebase.firestore.FirebaseFirestore
import com.google.firebase.firestore.Query
import com.google.firebase.storage.FirebaseStorage
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.callbackFlow
import kotlinx.coroutines.tasks.await
import mx.edu.utng.reciclaDH.data.model.Recompensa
import mx.edu.utng.reciclaDH.util.Resource
import javax.inject.Inject
import javax.inject.Singleton

/**
 * Repositorio para gestionar recompensas (becas, apoyos).
 * 
 * Las recompensas son creadas por administradores municipales.
 */
@Singleton
class RecompensaRepository @Inject constructor(
    private val firestore: FirebaseFirestore,
    private val storage: FirebaseStorage
) {
    
    /**
     * Obtiene todas las recompensas activas en tiempo real.
     * 
     * @return Flow con lista de recompensas ordenadas por puntos (menor a mayor)
     */
    fun obtenerRecompensasActivas(): Flow<Resource<List<Recompensa>>> = callbackFlow {
        trySend(Resource.Loading())
        
        val listener = firestore.collection(Recompensa.COLLECTION_NAME)
            .whereEqualTo("activa", true)
            .orderBy("costoEnPuntos", Query.Direction.ASCENDING)
            .addSnapshotListener { snapshot, error ->
                if (error != null) {
                    trySend(Resource.Error(error.message ?: "Error desconocido"))
                    return@addSnapshotListener
                }
                
                val recompensas = snapshot?.toObjects(Recompensa::class.java)
                    ?.filter { it.estaDisponible() } ?: emptyList()
                
                trySend(Resource.Success(recompensas))
            }
        
        awaitClose { listener.remove() }
    }
    
    /**
     * Obtiene una recompensa específica por ID.
     */
    suspend fun obtenerRecompensa(recompensaId: String): Resource<Recompensa> {
        return try {
            val doc = firestore.collection(Recompensa.COLLECTION_NAME)
                .document(recompensaId)
                .get()
                .await()
            
            val recompensa = doc.toObject(Recompensa::class.java)
                ?: return Resource.Error("Recompensa no encontrada")
            
            Resource.Success(recompensa)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al obtener recompensa")
        }
    }
    
    /**
     * Crea una nueva recompensa (solo admins).
     * 
     * @param recompensa Datos de la recompensa
     * @param imagenUri URI de imagen (opcional)
     * @param adminId ID del admin que la crea
     * 
     * @return Resource<String> con ID de la recompensa creada
     */
    suspend fun crearRecompensa(
        recompensa: Recompensa,
        imagenUri: Uri?,
        adminId: String
    ): Resource<String> {
        return try {
            // Subir imagen si existe
            val imagenUrl = if (imagenUri != null) {
                subirImagenRecompensa(imagenUri)
            } else null
            
            val recompensaConDatos = recompensa.copy(
                imagenUrl = imagenUrl,
                creadoPor = adminId
            )
            
            val docRef = firestore.collection(Recompensa.COLLECTION_NAME)
                .add(recompensaConDatos)
                .await()
            
            Resource.Success(docRef.id)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al crear recompensa")
        }
    }
    
    /**
     * Sube imagen de recompensa a Storage.
     */
    private suspend fun subirImagenRecompensa(imageUri: Uri): String {
        val timestamp = System.currentTimeMillis()
        val filename = "reward_${timestamp}.jpg"
        
        val storageRef = storage.reference
            .child("rewards")
            .child(filename)
        
        storageRef.putFile(imageUri).await()
        return storageRef.downloadUrl.await().toString()
    }
    
    /**
     * Actualiza una recompensa existente.
     */
    suspend fun actualizarRecompensa(
        recompensaId: String,
        updates: Map<String, Any>
    ): Resource<Unit> {
        return try {
            firestore.collection(Recompensa.COLLECTION_NAME)
                .document(recompensaId)
                .update(updates)
                .await()
            
            Resource.Success(Unit)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al actualizar recompensa")
        }
    }
    
    /**
     * Reduce el stock de una recompensa después de un canje.
     * 
     * Usa una transacción para evitar condiciones de carrera.
     */
    suspend fun reducirStock(recompensaId: String): Resource<Unit> {
        return try {
            firestore.runTransaction { transaction ->
                val docRef = firestore.collection(Recompensa.COLLECTION_NAME)
                    .document(recompensaId)
                
                val snapshot = transaction.get(docRef)
                val recompensa = snapshot.toObject(Recompensa::class.java)
                    ?: throw Exception("Recompensa no encontrada")
                
                // Verificar stock
                if (recompensa.cantidadDisponible == 0) {
                    throw Exception("Recompensa agotada")
                }
                
                // No reducir si es ilimitado
                if (recompensa.cantidadDisponible != Recompensa.STOCK_ILIMITADO) {
                    transaction.update(
                        docRef,
                        "cantidadDisponible",
                        recompensa.cantidadDisponible - 1
                    )
                }
            }.await()
            
            Resource.Success(Unit)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al reducir stock")
        }
    }
    
    /**
     * Aumenta el stock de una recompensa (al cancelar un canje).
     */
    suspend fun aumentarStock(recompensaId: String): Resource<Unit> {
        return try {
            firestore.runTransaction { transaction ->
                val docRef = firestore.collection(Recompensa.COLLECTION_NAME)
                    .document(recompensaId)
                
                val snapshot = transaction.get(docRef)
                val recompensa = snapshot.toObject(Recompensa::class.java)
                    ?: throw Exception("Recompensa no encontrada")
                
                // No aumentar si es ilimitado
                if (recompensa.cantidadDisponible != Recompensa.STOCK_ILIMITADO) {
                    transaction.update(
                        docRef,
                        "cantidadDisponible",
                        recompensa.cantidadDisponible + 1
                    )
                }
            }.await()
            
            Resource.Success(Unit)
            
        } catch (e: Exception) {
            Resource.Error(e.message ?: "Error al aumentar stock")
        }
    }
    
    /**
     * Desactiva una recompensa (soft delete).
     */
    suspend fun desactivarRecompensa(recompensaId: String): Resource<Unit> {
        return actualizarRecompensa(recompensaId, mapOf("activa" to false))
    }
    
    /**
     * Activa una recompensa desactivada.
     */
    suspend fun activarRecompensa(recompensaId: String): Resource<Unit> {
        return actualizarRecompensa(recompensaId, mapOf("activa" to true))
    }
}
```

---

## **PARTE 8: VIEWMODELS (Lógica de Negocio)**

### 8.1 LoginViewModel

📁 **ui/screens/auth/LoginViewModel.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.screens.auth

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.launch
import mx.edu.utng.reciclaDH.data.model.Usuario
import mx.edu.utng.reciclaDH.data.repository.AuthRepository
import mx.edu.utng.reciclaDH.util.Resource
import javax.inject.Inject

/**
 * ViewModel para la pantalla de Login.
 * 
 * Responsabilidades:
 * - Validar campos de email y contraseña
 * - Iniciar sesión con email/password
 * - Iniciar sesión con Google
 * - Recuperar contraseña
 * - Mantener estado de la UI
 * 
 * Los ViewModel sobreviven a cambios de configuración (rotación de pantalla).
 */
@HiltViewModel
class LoginViewModel @Inject constructor(
    private val authRepository: AuthRepository
) : ViewModel() {
    
    // ========== ESTADO DE LA UI ==========
    
    /**
     * Email ingresado por el usuario.
     */
    private val _email = MutableStateFlow("")
    val email: StateFlow<String> = _email.asStateFlow()
    
    /**
     * Contraseña ingresada por el usuario.
     */
    private val _password = MutableStateFlow("")
    val password: StateFlow<String> = _password.asStateFlow()
    
    /**
     * Si la contraseña es visible o está oculta.
     */
    private val _passwordVisible = MutableStateFlow(false)
    val passwordVisible: StateFlow<Boolean> = _passwordVisible.asStateFlow()
    
    /**
     * Estado del proceso de login.
     */
    private val _loginState = MutableStateFlow<Resource<Usuario>?>(null)
    val loginState: StateFlow<Resource<Usuario>?> = _loginState.asStateFlow()
    
    /**
     * Mensaje de error de validación.
     */
    private val _errorMessage = MutableStateFlow<String?>(null)
    val errorMessage: StateFlow<String?> = _errorMessage.asStateFlow()
    
    // ========== ACCIONES DEL USUARIO ==========
    
    /**
     * Actualiza el email.
     */
    fun onEmailChange(newEmail: String) {
        _email.value = newEmail
        _errorMessage.value = null // Limpiar error al escribir
    }
    
    /**
     * Actualiza la contraseña.
     */
    fun onPasswordChange(newPassword: String) {
        _password.value = newPassword
        _errorMessage.value = null
    }
    
    /**
     * Alterna visibilidad de la contraseña.
     */
    fun togglePasswordVisibility() {
        _passwordVisible.value = !_passwordVisible.value
    }
    
    /**
     * Valida los campos antes de iniciar sesión.
     * 
     * @return true si los campos son válidos
     */
    private fun validarCampos(): Boolean {
        when {
            _email.value.isBlank() -> {
                _errorMessage.value = "Ingresa tu correo electrónico"
                return false
            }
            !android.util.Patterns.EMAIL_ADDRESS.matcher(_email.value).matches() -> {
                _errorMessage.value = "Correo electrónico inválido"
                return false
            }
            _password.value.isBlank() -> {
                _errorMessage.value = "Ingresa tu contraseña"
                return false
            }
            _password.value.length < 6 -> {
                _errorMessage.value = "La contraseña debe tener al menos 6 caracteres"
                return false
            }
        }
        return true
    }
    
    /**
     * Inicia sesión con email y contraseña.
     */
    fun iniciarSesion() {
        // Validar campos
        if (!validarCampos()) return
        
        viewModelScope.launch {
            _loginState.value = Resource.Loading()
            
            val result = authRepository.iniciarSesion(
                email = _email.value.trim(),
                password = _password.value
            )
            
            _loginState.value = result
        }
    }
    
    /**
     * Inicia sesión con Google.
     * 
     * @param idToken Token obtenido de Google Sign-In
     */
    fun iniciarSesionConGoogle(idToken: String) {
        viewModelScope.launch {
            _loginState.value = Resource.Loading()
            
            val result = authRepository.iniciarSesionConGoogle(idToken)
            
            _loginState.value = result
        }
    }
    
    /**
     * Envía email para recuperar contraseña.
     */
    fun recuperarPassword() {
        if (_email.value.isBlank()) {
            _errorMessage.value = "Ingresa tu correo para recuperar la contraseña"
            return
        }
        
        if (!android.util.Patterns.EMAIL_ADDRESS.matcher(_email.value).matches()) {
            _errorMessage.value = "Correo electrónico inválido"
            return
        }
        
        viewModelScope.launch {
            when (val result = authRepository.

Continuar

enviarEmailRecuperacion(_email.value.trim())) {
is Resource.Success -> {
_errorMessage.value = "Se envió un correo para restablecer tu contraseña"
}
is Resource.Error -> {
_errorMessage.value = result.message
}
else -> {}
}
}
}

/**
 * Limpia el estado de login (después de navegar).
 */
fun limpiarEstado() {
    _loginState.value = null
    _errorMessage.value = null
}

/**
 * Limpia todos los campos.
 */
fun limpiarCampos() {
    _email.value = ""
    _password.value = ""
    _passwordVisible.value = false
    _errorMessage.value = null
}

}


```
---

### 8.2 HomeViewModel

📁 **ui/screens/home/HomeViewModel.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.screens.home

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.flow.*
import kotlinx.coroutines.launch
import mx.edu.utng.reciclaDH.data.model.Entrega
import mx.edu.utng.reciclaDH.data.model.Usuario
import mx.edu.utng.reciclaDH.data.repository.AuthRepository
import mx.edu.utng.reciclaDH.data.repository.EntregaRepository
import mx.edu.utng.reciclaDH.data.repository.UsuarioRepository
import mx.edu.utng.reciclaDH.util.Resource
import javax.inject.Inject

/**
 * ViewModel para la pantalla de inicio.
 * 
 * Muestra:
 * - Información del usuario (nombre, puntos)
 * - Últimas entregas
 * - Estadísticas rápidas
 */
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val authRepository: AuthRepository,
    private val usuarioRepository: UsuarioRepository,
    private val entregaRepository: EntregaRepository
) : ViewModel() {
    
    /**
     * ID del usuario actual.
     */
    private val userId: String = authRepository.currentUserId ?: ""
    
    /**
     * Datos del usuario en tiempo real.
     * 
     * Se actualiza automáticamente cuando cambian los puntos u otros datos.
     */
    val usuario: StateFlow<Resource<Usuario>> = usuarioRepository
        .obtenerUsuarioFlow(userId)
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = Resource.Loading()
        )
    
    /**
     * Últimas 5 entregas del usuario.
     */
    val ultimasEntregas: StateFlow<Resource<List<Entrega>>> = entregaRepository
        .obtenerEntregasUsuario(userId)
        .map { resource ->
            when (resource) {
                is Resource.Success -> {
                    // Tomar solo las 5 más recientes
                    Resource.Success(resource.data?.take(5) ?: emptyList())
                }
                is Resource.Error -> resource
                is Resource.Loading -> resource
            }
        }
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = Resource.Loading()
        )
    
    /**
     * Estadísticas del usuario (entregas totales, aprobadas, etc.).
     */
    private val _estadisticas = MutableStateFlow<Resource<Map<String, Int>>>(Resource.Loading())
    val estadisticas: StateFlow<Resource<Map<String, Int>>> = _estadisticas.asStateFlow()
    
    init {
        cargarEstadisticas()
    }
    
    /**
     * Carga estadísticas del usuario.
     */
    private fun cargarEstadisticas() {
        viewModelScope.launch {
            _estadisticas.value = Resource.Loading()
            _estadisticas.value = entregaRepository.obtenerEstadisticasUsuario(userId)
        }
    }
    
    /**
     * Recarga todas las datos (pull to refresh).
     */
    fun recargar() {
        cargarEstadisticas()
        // Los flows se recargan automáticamente
    }
    
    /**
     * Cierra sesión del usuario.
     */
    fun cerrarSesion() {
        authRepository.cerrarSesion()
    }
}
```

---

### 8.3 EntregaViewModel

📁 **ui/screens/entrega/EntregaViewModel.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.screens.entrega

import android.net.Uri
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.launch
import mx.edu.utng.reciclaDH.data.model.Entrega
import mx.edu.utng.reciclaDH.data.model.TipoMaterial
import mx.edu.utng.reciclaDH.data.repository.AuthRepository
import mx.edu.utng.reciclaDH.data.repository.EntregaRepository
import mx.edu.utng.reciclaDH.data.repository.UsuarioRepository
import mx.edu.utng.reciclaDH.util.Resource
import javax.inject.Inject

/**
 * ViewModel para registrar una nueva entrega de material reciclable.
 * 
 * Flujo:
 * 1. Usuario selecciona tipo de material
 * 2. Ingresa peso en kg
 * 3. Toma/selecciona foto (opcional)
 * 4. Agrega comentarios (opcional)
 * 5. Envía entrega
 */
@HiltViewModel
class EntregaViewModel @Inject constructor(
    private val authRepository: AuthRepository,
    private val entregaRepository: EntregaRepository,
    private val usuarioRepository: UsuarioRepository
) : ViewModel() {
    
    // ========== ESTADO DEL FORMULARIO ==========
    
    /**
     * Tipo de material seleccionado.
     */
    private val _tipoMaterial = MutableStateFlow(TipoMaterial.PET)
    val tipoMaterial: StateFlow<TipoMaterial> = _tipoMaterial.asStateFlow()
    
    /**
     * Peso en kg (como String para el TextField).
     */
    private val _pesoText = MutableStateFlow("")
    val pesoText: StateFlow<String> = _pesoText.asStateFlow()
    
    /**
     * URI de la imagen seleccionada.
     */
    private val _imagenUri = MutableStateFlow<Uri?>(null)
    val imagenUri: StateFlow<Uri?> = _imagenUri.asStateFlow()
    
    /**
     * Comentarios adicionales.
     */
    private val _comentarios = MutableStateFlow("")
    val comentarios: StateFlow<String> = _comentarios.asStateFlow()
    
    /**
     * Puntos que se generarán (calculados automáticamente).
     */
    private val _puntosEstimados = MutableStateFlow(0)
    val puntosEstimados: StateFlow<Int> = _puntosEstimados.asStateFlow()
    
    /**
     * Estado del proceso de creación.
     */
    private val _crearState = MutableStateFlow<Resource<String>?>(null)
    val crearState: StateFlow<Resource<String>?> = _crearState.asStateFlow()
    
    /**
     * Mensaje de error de validación.
     */
    private val _errorMessage = MutableStateFlow<String?>(null)
    val errorMessage: StateFlow<String?> = _errorMessage.asStateFlow()
    
    // ========== ID Y NOMBRE DEL USUARIO ==========
    
    private val userId = authRepository.currentUserId ?: ""
    private var nombreUsuario = ""
    
    init {
        cargarNombreUsuario()
    }
    
    /**
     * Carga el nombre del usuario para incluirlo en la entrega.
     */
    private fun cargarNombreUsuario() {
        viewModelScope.launch {
            when (val result = usuarioRepository.obtenerUsuario(userId)) {
                is Resource.Success -> {
                    nombreUsuario = result.data?.nombre ?: ""
                }
                else -> {}
            }
        }
    }
    
    // ========== ACCIONES DEL USUARIO ==========
    
    /**
     * Actualiza el tipo de material.
     */
    fun onTipoMaterialChange(tipo: TipoMaterial) {
        _tipoMaterial.value = tipo
        calcularPuntosEstimados()
    }
    
    /**
     * Actualiza el peso.
     */
    fun onPesoChange(nuevoPeso: String) {
        // Solo permitir números y un punto decimal
        val filtrado = nuevoPeso.filter { it.isDigit() || it == '.' }
        
        // Evitar múltiples puntos decimales
        if (filtrado.count { it == '.' } <= 1) {
            _pesoText.value = filtrado
            calcularPuntosEstimados()
            _errorMessage.value = null
        }
    }
    
    /**
     * Actualiza la URI de la imagen.
     */
    fun onImagenChange(uri: Uri?) {
        _imagenUri.value = uri
    }
    
    /**
     * Actualiza los comentarios.
     */
    fun onComentariosChange(nuevosComentarios: String) {
        _comentarios.value = nuevosComentarios
    }
    
    /**
     * Calcula puntos estimados basados en peso y tipo de material.
     */
    private fun calcularPuntosEstimados() {
        val peso = _pesoText.value.toDoubleOrNull() ?: 0.0
        val puntosPorKg = _tipoMaterial.value.puntosPorKg
        _puntosEstimados.value = (peso * puntosPorKg).toInt()
    }
    
    /**
     * Valida el formulario antes de enviar.
     */
    private fun validarFormulario(): Boolean {
        val peso = _pesoText.value.toDoubleOrNull()
        
        when {
            peso == null || peso <= 0 -> {
                _errorMessage.value = "Ingresa un peso válido"
                return false
            }
            peso < 0.1 -> {
                _errorMessage.value = "El peso mínimo es 0.1 kg"
                return false
            }
            peso > 1000 -> {
                _errorMessage.value = "El peso máximo es 1000 kg"
                return false
            }
        }
        
        return true
    }
    
    /**
     * Crea y envía la entrega.
     */
    fun crearEntrega() {
        if (!validarFormulario()) return
        
        viewModelScope.launch {
            _crearState.value = Resource.Loading()
            
            val peso = _pesoText.value.toDouble()
            
            val entrega = Entrega(
                usuarioId = userId,
                usuarioNombre = nombreUsuario,
                tipoMaterial = _tipoMaterial.value,
                pesoKg = peso,
                comentarios = _comentarios.value.trim()
            )
            
            val result = entregaRepository.crearEntrega(
                entrega = entrega,
                imagenUri = _imagenUri.value
            )
            
            _crearState.value = result
        }
    }
    
    /**
     * Limpia el formulario después de crear exitosamente.
     */
    fun limpiarFormulario() {
        _tipoMaterial.value = TipoMaterial.PET
        _pesoText.value = ""
        _imagenUri.value = null
        _comentarios.value = ""
        _puntosEstimados.value = 0
        _crearState.value = null
        _errorMessage.value = null
    }
}
```

---

## **PARTE 9: COMPONENTES UI REUTILIZABLES**

### 9.1 ReciclaButton (Botón personalizado)

📁 **ui/components/ReciclaButton.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.components

import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

/**
 * Botón personalizado de la app.
 * 
 * Encapsula el estilo común de todos los botones
 * para mantener consistencia visual.
 * 
 * Ejemplo de uso:
 * ```
 * ReciclaButton(
 *     text = "Iniciar Sesión",
 *     onClick = { viewModel.login() },
 *     isLoading = isLoading
 * )
 * ```
 */
@Composable
fun ReciclaButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    isLoading: Boolean = false,
    colors: ButtonColors = ButtonDefaults.buttonColors()
) {
    Button(
        onClick = onClick,
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp),
        enabled = enabled && !isLoading,
        colors = colors,
        shape = MaterialTheme.shapes.medium
    ) {
        if (isLoading) {
            // Mostrar indicador de carga
            CircularProgressIndicator(
                modifier = Modifier.size(24.dp),
                color = Color.White,
                strokeWidth = 2.dp
            )
        } else {
            // Mostrar texto del botón
            Text(
                text = text,
                style = MaterialTheme.typography.titleMedium
            )
        }
    }
}

/**
 * Botón secundario (outlined).
 */
@Composable
fun ReciclaOutlinedButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true
) {
    OutlinedButton(
        onClick = onClick,
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp),
        enabled = enabled,
        shape = MaterialTheme.shapes.medium
    ) {
        Text(
            text = text,
            style = MaterialTheme.typography.titleMedium
        )
    }
}
```

---

### 9.2 ReciclaTextField (Campo de texto personalizado)

📁 **ui/components/ReciclaTextField.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.components

import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.text.KeyboardActions
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.input.ImeAction
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.text.input.VisualTransformation

/**
 * TextField personalizado de la app.
 * 
 * Ventajas:
 * - Estilo consistente
 * - Manejo de errores integrado
 * - Configuración simplificada
 * 
 * Ejemplo de uso:
 * ```
 * ReciclaTextField(
 *     value = email,
 *     onValueChange = { viewModel.onEmailChange(it) },
 *     label = "Correo electrónico",
 *     keyboardType = KeyboardType.Email,
 *     isError = !email.isValidEmail()
 * )
 * ```
 */
@Composable
fun ReciclaTextField(
    value: String,
    onValueChange: (String) -> Unit,
    label: String,
    modifier: Modifier = Modifier,
    placeholder: String = "",
    leadingIcon: @Composable (() -> Unit)? = null,
    trailingIcon: @Composable (() -> Unit)? = null,
    isError: Boolean = false,
    errorMessage: String? = null,
    enabled: Boolean = true,
    readOnly: Boolean = false,
    singleLine: Boolean = true,
    maxLines: Int = 1,
    keyboardType: KeyboardType = KeyboardType.Text,
    imeAction: ImeAction = ImeAction.Next,
    keyboardActions: KeyboardActions = KeyboardActions.Default,
    visualTransformation: VisualTransformation = VisualTransformation.None
) {
    OutlinedTextField(
        value = value,
        onValueChange = onValueChange,
        label = { Text(label) },
        placeholder = { Text(placeholder) },
        modifier = modifier.fillMaxWidth(),
        leadingIcon = leadingIcon,
        trailingIcon = trailingIcon,
        isError = isError,
        supportingText = {
            if (isError && errorMessage != null) {
                Text(
                    text = errorMessage,
                    color = MaterialTheme.colorScheme.error
                )
            }
        },
        enabled = enabled,
        readOnly = readOnly,
        singleLine = singleLine,
        maxLines = maxLines,
        keyboardOptions = KeyboardOptions(
            keyboardType = keyboardType,
            imeAction = imeAction
        ),
        keyboardActions = keyboardActions,
        visualTransformation = visualTransformation,
        shape = MaterialTheme.shapes.medium
    )
}
```

---

### 9.3 LoadingDialog (Diálogo de carga)

📁 **ui/components/LoadingDialog.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.components

import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.compose.ui.window.Dialog
import androidx.compose.ui.window.DialogProperties

/**
 * Diálogo que muestra un indicador de carga.
 * 
 * Se usa cuando se está procesando algo y queremos
 * bloquear la interacción con la UI.
 * 
 * Ejemplo de uso:
 * ```
 * if (isLoading) {
 *     LoadingDialog(message = "Iniciando sesión...")
 * }
 * ```
 */
@Composable
fun LoadingDialog(
    message: String = "Cargando...",
    dismissible: Boolean = false
) {
    Dialog(
        onDismissRequest = { },
        properties = DialogProperties(
            dismissOnBackPress = dismissible,
            dismissOnClickOutside = dismissible
        )
    ) {
        Card(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            shape = MaterialTheme.shapes.large
        ) {
            Column(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(24.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(16.dp)
            ) {
                CircularProgressIndicator()
                
                Text(
                    text = message,
                    style = MaterialTheme.typography.bodyLarge
                )
            }
        }
    }
}
```

---

### 9.4 ErrorMessage (Mensaje de error)

📁 **ui/components/ErrorMessage.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.components

import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Error
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp

/**
 * Componente para mostrar mensajes de error de forma consistente.
 * 
 * Ejemplo de uso:
 * ```
 * when (val state = uiState) {
 *     is Resource.Error -> ErrorMessage(
 *         message = state.message ?: "Error desconocido",
 *         onRetry = { viewModel.retry() }
 *     )
 * }
 * ```
 */
@Composable
fun ErrorMessage(
    message: String,
    modifier: Modifier = Modifier,
    onRetry: (() -> Unit)? = null
) {
    Card(
        modifier = modifier
            .fillMaxWidth()
            .padding(16.dp),
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.errorContainer
        )
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(12.dp)
        ) {
            Icon(
                imageVector = Icons.Default.Error,
                contentDescription = "Error",
                tint = MaterialTheme.colorScheme.error,
                modifier = Modifier.size(48.dp)
            )
            
            Text(
                text = message,
                style = MaterialTheme.typography.bodyLarge,
                color = MaterialTheme.colorScheme.onErrorContainer,
                textAlign = TextAlign.Center
            )
            
            if (onRetry != null) {
                TextButton(onClick = onRetry) {
                    Text("Reintentar")
                }
            }
        }
    }
}

/**
 * Pantalla completa de error.
 */
@Composable
fun ErrorScreen(
    message: String,
    onRetry: () -> Unit,
    modifier: Modifier = Modifier
) {
    Box(
        modifier = modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        ErrorMessage(
            message = message,
            onRetry = onRetry
        )
    }
}
```

---

## **PARTE 10: PANTALLAS (Screens)**

### 10.1 LoginScreen

📁 **ui/screens/auth/LoginScreen.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.screens.auth

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Email
import androidx.compose.material.icons.filled.Lock
import androidx.compose.material.icons.filled.Visibility
import androidx.compose.material.icons.filled.VisibilityOff
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.text.input.PasswordVisualTransformation
import androidx.compose.ui.text.input.VisualTransformation
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.hilt.navigation.compose.hiltViewModel
import mx.edu.utng.reciclaDH.ui.components.*
import mx.edu.utng.reciclaDH.util.Resource

/**
 * Pantalla de inicio de sesión.
 * 
 * Permite al usuario:
 * - Iniciar sesión con email/contraseña
 * - Iniciar sesión con Google
 * - Recuperar contraseña
 * - Navegar a registro
 */
@Composable
fun LoginScreen(
    onNavigateToRegister: () -> Unit,
    onNavigateToHome: () -> Unit,
    viewModel: LoginViewModel = hiltViewModel()
) {
    // Recolectar estados del ViewModel
    val email by viewModel.email.collectAsState()
    val password by viewModel.password.collectAsState()
    val passwordVisible by viewModel.passwordVisible.collectAsState()
    val loginState by viewModel.loginState.collectAsState()
    val errorMessage by viewModel.errorMessage.collectAsState()
    
    // Manejar resultado del login
    LaunchedEffect(loginState) {
        when (loginState) {
            is Resource.Success -> {
                viewModel.limpiarEstado()
                onNavigateToHome()
            }
            else -> {}
        }
    }
    
    // Mostrar diálogo de carga
    if (loginState is Resource.Loading) {
        LoadingDialog(message = "Iniciando sesión...")
    }
    
    // UI
    Column(
        modifier = Modifier
            .fillMaxSize()
            .verticalScroll(rememberScrollState())
            .padding(24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        // Logo o título
        Text(
            text = "♻️ ReciclaD Hidalgo",
            style = MaterialTheme.typography.headlineLarge,
            color = MaterialTheme.colorScheme.primary
        )
        
        Spacer(modifier = Modifier.height(8.dp))
        
        Text(
            text = "Recicla y gana recompensas",
            style = MaterialTheme.typography.bodyLarge,
            textAlign = TextAlign.Center
        )
        
        Spacer(modifier = Modifier.height(32.dp))
        
        // Campo de email
        ReciclaTextField(
            value = email,
            onValueChange = { viewModel.onEmailChange(it) },
            label = "Correo electrónico",
            keyboardType = KeyboardType.Email,
            leadingIcon = {
                Icon(Icons.Default.Email, contentDescription = null)
            }
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Campo de contraseña
        ReciclaTextField(
            value = password,
            onValueChange = { viewModel.onPasswordChange(it) },
            label = "Contraseña",
            keyboardType = KeyboardType.Password,
            visualTransformation = if (passwordVisible) {
                VisualTransformation.None
            } else {
                PasswordVisualTransformation()
            },
            leadingIcon = {
                Icon(Icons.Default.Lock, contentDescription = null)
            },
            trailingIcon = {
                IconButton(onClick = { viewModel.togglePasswordVisibility() }) {
                    Icon(
                        imageVector = if (passwordVisible) {
                            Icons.Default.Visibility
                        } else {
                            Icons.Default.VisibilityOff
                        },
                        contentDescription = if (passwordVisible) {
                            "Ocultar contraseña"
                        } else {
                            "Mostrar contraseña"
                        }
                    )
                }
            }
        )
        
        // Mensaje de error
        if (errorMessage != null) {
            Spacer(modifier = Modifier.height(8.dp))
            Text(
                text = errorMessage!!,
                color = MaterialTheme.colorScheme.error,
                style = MaterialTheme.typography.bodySmall
            )
        }
        
        // Recuperar contraseña
        TextButton(
            onClick = { viewModel.recuperarPassword() },
            modifier = Modifier.align(Alignment.End)
        ) {
            Text("¿Olvidaste tu contraseña?")
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // Botón de login
        ReciclaButton(
            text = "Iniciar Sesión",
            onClick = { viewModel.iniciarSesion() },
            isLoading = loginState is Resource.Loading
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Divider
        Row(
            modifier = Modifier.fillMaxWidth(),
            verticalAlignment = Alignment.CenterVertically
        ) {
            HorizontalDivider(modifier = Modifier.weight(1f))
            Text(
                text = "  o  ",
                style = MaterialTheme.typography.bodySmall
            )
            HorizontalDivider(modifier = Modifier.weight(1f))
        }
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Botón de Google (implementación completa requiere configuración adicional)
        ReciclaOutlinedButton(
            text = "Continuar con Google",
            onClick = { /* TODO: Implementar Google Sign-In */ }
        )
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // Link a registro
        Row(
            verticalAlignment = Alignment.CenterVertically
        ) {
            Text("¿No tienes cuenta?")
            TextButton(onClick = onNavigateToRegister) {
                Text("Regístrate")
            }
        }
    }
}
```

---

✅ Conceptos fundamentales explicados para estudiantes
✅ Configuración completa del proyecto
✅ Arquitectura MVVM con Repository Pattern
✅ Modelos de datos completos (Usuario, Entrega, Recompensa, Canje)
✅ Utilidades (Resource, Extensions, Constants)
✅ Repositorios completos con Firebase
✅ ViewModels con lógica de negocio
✅ Componentes UI reutilizables
✅ Pantalla de Login completa


11.1 RegisterScreen (Pantalla de Registro)

📁 ui/screens/auth/RegisterScreen.kt

```kotlin

package mx.edu.utng.reciclaDH.ui.screens.auth

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.text.input.PasswordVisualTransformation
import androidx.compose.ui.text.input.VisualTransformation
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.hilt.navigation.compose.hiltViewModel
import mx.edu.utng.reciclaDH.ui.components.*
import mx.edu.utng.reciclaDH.util.Resource

/**
 * Pantalla de registro de nuevos usuarios.
 */
@Composable
fun RegisterScreen(
    onNavigateToLogin: () -> Unit,
    onNavigateToHome: () -> Unit,
    viewModel: RegisterViewModel = hiltViewModel()
) {
    val nombre by viewModel.nombre.collectAsState()
    val email by viewModel.email.collectAsState()
    val telefono by viewModel.telefono.collectAsState()
    val password by viewModel.password.collectAsState()
    val confirmPassword by viewModel.confirmPassword.collectAsState()
    val passwordVisible by viewModel.passwordVisible.collectAsState()
    val registerState by viewModel.registerState.collectAsState()
    val errorMessage by viewModel.errorMessage.collectAsState()
    
    // Manejar resultado del registro
    LaunchedEffect(registerState) {
        when (registerState) {
            is Resource.Success -> {
                viewModel.limpiarEstado()
                onNavigateToHome()
            }
            else -> {}
        }
    }
    
    // Diálogo de carga
    if (registerState is Resource.Loading) {
        LoadingDialog(message = "Creando cuenta...")
    }
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .verticalScroll(rememberScrollState())
            .padding(24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        // Título
        Text(
            text = "Crear Cuenta",
            style = MaterialTheme.typography.headlineLarge,
            color = MaterialTheme.colorScheme.primary
        )
        
        Spacer(modifier = Modifier.height(8.dp))
        
        Text(
            text = "Únete y comienza a reciclar",
            style = MaterialTheme.typography.bodyLarge,
            textAlign = TextAlign.Center
        )
        
        Spacer(modifier = Modifier.height(32.dp))
        
        // Campo: Nombre completo
        ReciclaTextField(
            value = nombre,
            onValueChange = { viewModel.onNombreChange(it) },
            label = "Nombre completo",
            keyboardType = KeyboardType.Text,
            leadingIcon = {
                Icon(Icons.Default.Person, contentDescription = null)
            }
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Campo: Email
        ReciclaTextField(
            value = email,
            onValueChange = { viewModel.onEmailChange(it) },
            label = "Correo electrónico",
            keyboardType = KeyboardType.Email,
            leadingIcon = {
                Icon(Icons.Default.Email, contentDescription = null)
            }
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Campo: Teléfono
        ReciclaTextField(
            value = telefono,
            onValueChange = { viewModel.onTelefonoChange(it) },
            label = "Teléfono (10 dígitos)",
            keyboardType = KeyboardType.Phone,
            leadingIcon = {
                Icon(Icons.Default.Phone, contentDescription = null)
            }
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Campo: Contraseña
        ReciclaTextField(
            value = password,
            onValueChange = { viewModel.onPasswordChange(it) },
            label = "Contraseña (mínimo 6 caracteres)",
            keyboardType = KeyboardType.Password,
            visualTransformation = if (passwordVisible) {
                VisualTransformation.None
            } else {
                PasswordVisualTransformation()
            },
            leadingIcon = {
                Icon(Icons.Default.Lock, contentDescription = null)
            },
            trailingIcon = {
                IconButton(onClick = { viewModel.togglePasswordVisibility() }) {
                    Icon(
                        imageVector = if (passwordVisible) {
                            Icons.Default.Visibility
                        } else {
                            Icons.Default.VisibilityOff
                        },
                        contentDescription = null
                    )
                }
            }
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Campo: Confirmar contraseña
        ReciclaTextField(
            value = confirmPassword,
            onValueChange = { viewModel.onConfirmPasswordChange(it) },
            label = "Confirmar contraseña",
            keyboardType = KeyboardType.Password,
            visualTransformation = if (passwordVisible) {
                VisualTransformation.None
            } else {
                PasswordVisualTransformation()
            },
            leadingIcon = {
                Icon(Icons.Default.Lock, contentDescription = null)
            }
        )
        
        // Mensaje de error
        if (errorMessage != null) {
            Spacer(modifier = Modifier.height(8.dp))
            Text(
                text = errorMessage!!,
                color = MaterialTheme.colorScheme.error,
                style = MaterialTheme.typography.bodySmall,
                textAlign = TextAlign.Center
            )
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // Botón de registro
        ReciclaButton(
            text = "Registrarse",
            onClick = { viewModel.registrar() },
            isLoading = registerState is Resource.Loading
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Link a login
        Row(
            verticalAlignment = Alignment.CenterVertically
        ) {
            Text("¿Ya tienes cuenta?")
            TextButton(onClick = onNavigateToLogin) {
                Text("Inicia sesión")
            }
        }
    }
}

```
11.2 RegisterViewModel

📁 ui/screens/auth/RegisterViewModel.kt

```kotlin

package mx.edu.utng.reciclaDH.ui.screens.auth

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.launch
import mx.edu.utng.reciclaDH.data.model.Usuario
import mx.edu.utng.reciclaDH.data.repository.AuthRepository
import mx.edu.utng.reciclaDH.util.Resource
import mx.edu.utng.reciclaDH.util.isValidEmail
import mx.edu.utng.reciclaDH.util.isValidPhone
import mx.edu.utng.reciclaDH.util.toTitleCase
import javax.inject.Inject

@HiltViewModel
class RegisterViewModel @Inject constructor(
    private val authRepository: AuthRepository
) : ViewModel() {
    
    // Estados del formulario
    private val _nombre = MutableStateFlow("")
    val nombre: StateFlow<String> = _nombre.asStateFlow()
    
    private val _email = MutableStateFlow("")
    val email: StateFlow<String> = _email.asStateFlow()
    
    private val _telefono = MutableStateFlow("")
    val telefono: StateFlow<String> = _telefono.asStateFlow()
    
    private val _password = MutableStateFlow("")
    val password: StateFlow<String> = _password.asStateFlow()
    
    private val _confirmPassword = MutableStateFlow("")
    val confirmPassword: StateFlow<String> = _confirmPassword.asStateFlow()
    
    private val _passwordVisible = MutableStateFlow(false)
    val passwordVisible: StateFlow<Boolean> = _passwordVisible.asStateFlow()
    
    private val _registerState = MutableStateFlow<Resource<Usuario>?>(null)
    val registerState: StateFlow<Resource<Usuario>?> = _registerState.asStateFlow()
    
    private val _errorMessage = MutableStateFlow<String?>(null)
    val errorMessage: StateFlow<String?> = _errorMessage.asStateFlow()
    
    // Acciones
    fun onNombreChange(newNombre: String) {
        _nombre.value = newNombre
        _errorMessage.value = null
    }
    
    fun onEmailChange(newEmail: String) {
        _email.value = newEmail
        _errorMessage.value = null
    }
    
    fun onTelefonoChange(newTelefono: String) {
        // Solo permitir números
        val filtrado = newTelefono.filter { it.isDigit() }.take(10)
        _telefono.value = filtrado
        _errorMessage.value = null
    }
    
    fun onPasswordChange(newPassword: String) {
        _password.value = newPassword
        _errorMessage.value = null
    }
    
    fun onConfirmPasswordChange(newConfirmPassword: String) {
        _confirmPassword.value = newConfirmPassword
        _errorMessage.value = null
    }
    
    fun togglePasswordVisibility() {
        _passwordVisible.value = !_passwordVisible.value
    }
    
    /**
     * Valida el formulario de registro.
     */
    private fun validarFormulario(): Boolean {
        when {
            _nombre.value.isBlank() -> {
                _errorMessage.value = "Ingresa tu nombre completo"
                return false
            }
            _nombre.value.length < 3 -> {
                _errorMessage.value = "El nombre debe tener al menos 3 caracteres"
                return false
            }
            _email.value.isBlank() -> {
                _errorMessage.value = "Ingresa tu correo electrónico"
                return false
            }
            !_email.value.isValidEmail() -> {
                _errorMessage.value = "Correo electrónico inválido"
                return false
            }
            _telefono.value.isBlank() -> {
                _errorMessage.value = "Ingresa tu teléfono"
                return false
            }
            !_telefono.value.isValidPhone() -> {
                _errorMessage.value = "El teléfono debe tener 10 dígitos"
                return false
            }
            _password.value.isBlank() -> {
                _errorMessage.value = "Ingresa una contraseña"
                return false
            }
            _password.value.length < 6 -> {
                _errorMessage.value = "La contraseña debe tener al menos 6 caracteres"
                return false
            }
            _confirmPassword.value != _password.value -> {
                _errorMessage.value = "Las contraseñas no coinciden"
                return false
            }
        }
        return true
    }
    
    /**
     * Registra un nuevo usuario.
     */
    fun registrar() {
        if (!validarFormulario()) return
        
        viewModelScope.launch {
            _registerState.value = Resource.Loading()
            
            val result = authRepository.registrarUsuario(
                email = _email.value.trim(),
                password = _password.value,
                nombre = _nombre.value.trim().toTitleCase(),
                telefono = _telefono.value
            )
            
            _registerState.value = result
        }
    }
    
    fun limpiarEstado() {
        _registerState.value = null
        _errorMessage.value = null
    }
}

```
11.3 HomeScreen (Pantalla Principal)

📁 ui/screens/home/HomeScreen.kt

```kotlin

package mx.edu.utng.reciclaDH.ui.screens.home

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.hilt.navigation.compose.hiltViewModel
import com.google.accompanist.swiperefresh.SwipeRefresh
import com.google.accompanist.swiperefresh.rememberSwipeRefreshState
import mx.edu.utng.reciclaDH.data.model.Entrega
import mx.edu.utng.reciclaDH.data.model.EstadoEntrega
import mx.edu.utng.reciclaDH.ui.components.*
import mx.edu.utng.reciclaDH.util.Resource
import mx.edu.utng.reciclaDH.util.formatPoints
import mx.edu.utng.reciclaDH.util.toFormattedString
import mx.edu.utng.reciclaDH.util.toRelativeTime

/**
 * Pantalla principal de la app.
 * 
 * Muestra:
 * - Puntos del usuario
 * - Estadísticas rápidas
 * - Últimas entregas
 * - Accesos rápidos
 */
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun HomeScreen(
    onNavigateToPerfil: () -> Unit,
    onNavigateToEntregas: () -> Unit,
    onNavigateToRecompensas: () -> Unit,
    onNavigateToNuevaEntrega: () -> Unit,
    onNavigateToHistorial: () -> Unit,
    onLogout: () -> Unit,
    viewModel: HomeViewModel = hiltViewModel()
) {
    val usuarioState by viewModel.usuario.collectAsState()
    val entregasState by viewModel.ultimasEntregas.collectAsState()
    val estadisticasState by viewModel.estadisticas.collectAsState()
    
    var showLogoutDialog by remember { mutableStateOf(false) }
    var isRefreshing by remember { mutableStateOf(false) }
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("ReciclaD Hidalgo") },
                actions = {
                    IconButton(onClick = onNavigateToPerfil) {
                        Icon(Icons.Default.Person, contentDescription = "Perfil")
                    }
                    IconButton(onClick = { showLogoutDialog = true }) {
                        Icon(Icons.Default.ExitToApp, contentDescription = "Cerrar sesión")
                    }
                }
            )
        },
        floatingActionButton = {
            ExtendedFloatingActionButton(
                onClick = onNavigateToNuevaEntrega,
                icon = { Icon(Icons.Default.Add, contentDescription = null) },
                text = { Text("Nueva Entrega") }
            )
        }
    ) { paddingValues ->
        
        SwipeRefresh(
            state = rememberSwipeRefreshState(isRefreshing),
            onRefresh = {
                isRefreshing = true
                viewModel.recargar()
                isRefreshing = false
            },
            modifier = Modifier.padding(paddingValues)
        ) {
            LazyColumn(
                modifier = Modifier.fillMaxSize(),
                contentPadding = PaddingValues(16.dp),
                verticalArrangement = Arrangement.spacedBy(16.dp)
            ) {
                // Tarjeta de puntos
                item {
                    TarjetaPuntos(usuarioState)
                }
                
                // Estadísticas rápidas
                item {
                    EstadisticasRapidas(estadisticasState)
                }
                
                // Accesos rápidos
                item {
                    AccesosRapidos(
                        onNavigateToRecompensas = onNavigateToRecompensas,
                        onNavigateToHistorial = onNavigateToHistorial,
                        onNavigateToEntregas = onNavigateToEntregas
                    )
                }
                
                // Últimas entregas
                item {
                    Text(
                        text = "Últimas Entregas",
                        style = MaterialTheme.typography.titleLarge,
                        fontWeight = FontWeight.Bold
                    )
                }
                
                when (entregasState) {
                    is Resource.Loading -> {
                        item {
                            Box(
                                modifier = Modifier
                                    .fillMaxWidth()
                                    .height(200.dp),
                                contentAlignment = Alignment.Center
                            ) {
                                CircularProgressIndicator()
                            }
                        }
                    }
                    is Resource.Success -> {
                        val entregas = (entregasState as Resource.Success).data ?: emptyList()
                        if (entregas.isEmpty()) {
                            item {
                                EmptyStateMessage(
                                    message = "No tienes entregas aún",
                                    actionText = "Crear primera entrega",
                                    onAction = onNavigateToNuevaEntrega
                                )
                            }
                        } else {
                            items(entregas) { entrega ->
                                TarjetaEntrega(entrega)
                            }
                        }
                    }
                    is Resource.Error -> {
                        item {
                            ErrorMessage(
                                message = (entregasState as Resource.Error).message 
                                    ?: "Error al cargar entregas",
                                onRetry = { viewModel.recargar() }
                            )
                        }
                    }
                    null -> {}
                }
            }
        }
    }
    
    // Diálogo de confirmación de cierre de sesión
    if (showLogoutDialog) {
        AlertDialog(
            onDismissRequest = { showLogoutDialog = false },
            title = { Text("Cerrar Sesión") },
            text = { Text("¿Estás seguro que deseas cerrar sesión?") },
            confirmButton = {
                TextButton(
                    onClick = {
                        viewModel.cerrarSesion()
                        onLogout()
                    }
                ) {
                    Text("Sí, cerrar sesión")
                }
            },
            dismissButton = {
                TextButton(onClick = { showLogoutDialog = false }) {
                    Text("Cancelar")
                }
            }
        )
    }
}

/**
 * Tarjeta que muestra los puntos del usuario.
 */
@Composable
private fun TarjetaPuntos(usuarioState: Resource<mx.edu.utng.reciclaDH.data.model.Usuario>) {
    Card(
        modifier = Modifier.fillMaxWidth(),
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.primaryContainer
        )
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(24.dp),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            when (usuarioState) {
                is Resource.Loading -> {
                    CircularProgressIndicator()
                }
                is Resource.Success -> {
                    val usuario = usuarioState.data
                    Text(
                        text = "¡Hola, ${usuario?.nombre?.split(" ")?.first() ?: "Usuario"}!",
                        style = MaterialTheme.typography.titleLarge
                    )
                    
                    Spacer(modifier = Modifier.height(8.dp))
                    
                    Text(
                        text = (usuario?.puntos ?: 0).formatPoints(),
                        style = MaterialTheme.typography.displayLarge,
                        fontWeight = FontWeight.Bold,
                        color = MaterialTheme.colorScheme.primary
                    )
                    
                    Text(
                        text = "Puntos acumulados",
                        style = MaterialTheme.typography.bodyMedium
                    )
                }
                is Resource.Error -> {
                    Text(
                        text = "Error al cargar puntos",
                        color = MaterialTheme.colorScheme.error
                    )
                }
                null -> {}
            }
        }
    }
}

/**
 * Estadísticas rápidas en tarjetas pequeñas.
 */
@Composable
private fun EstadisticasRapidas(
    estadisticasState: Resource<Map<String, Int>>
) {
    when (estadisticasState) {
        is Resource.Success -> {
            val stats = estadisticasState.data ?: emptyMap()
            
            Row(
                modifier = Modifier.fillMaxWidth(),
                horizontalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                // Total entregas
                MiniStatCard(
                    modifier = Modifier.weight(1f),
                    valor = stats["totalEntregas"] ?: 0,
                    titulo = "Entregas",
                    icono = Icons.Default.Recycling
                )
                
                // Aprobadas
                MiniStatCard(
                    modifier = Modifier.weight(1f),
                    valor = stats["aprobadas"] ?: 0,
                    titulo = "Aprobadas",
                    icono = Icons.Default.CheckCircle,
                    color = MaterialTheme.colorScheme.tertiary
                )
                
                // Pendientes
                MiniStatCard(
                    modifier = Modifier.weight(1f),
                    valor = stats["pendientes"] ?: 0,
                    titulo = "Pendientes",
                    icono = Icons.Default.HourglassEmpty
                )
            }
        }
        else -> {}
    }
}

/**
 * Mini tarjeta de estadística.
 */
@Composable
private fun MiniStatCard(
    valor: Int,
    titulo: String,
    icono: androidx.compose.ui.graphics.vector.ImageVector,
    modifier: Modifier = Modifier,
    color: androidx.compose.ui.graphics.Color = MaterialTheme.colorScheme.secondary
) {
    Card(
        modifier = modifier,
        colors = CardDefaults.cardColors(
            containerColor = color.copy(alpha = 0.1f)
        )
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(12.dp),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Icon(
                imageVector = icono,
                contentDescription = null,
                tint = color
            )
            
            Spacer(modifier = Modifier.height(4.dp))
            
            Text(
                text = valor.toString(),
                style = MaterialTheme.typography.titleLarge,
                fontWeight = FontWeight.Bold,
                color = color
            )
            
            Text(
                text = titulo,
                style = MaterialTheme.typography.bodySmall
            )
        }
    }
}

/**
 * Accesos rápidos a secciones principales.
 */
@Composable
private fun AccesosRapidos(
    onNavigateToRecompensas: () -> Unit,
    onNavigateToHistorial: () -> Unit,
    onNavigateToEntregas: () -> Unit
) {
    Column {
        Text(
            text = "Accesos Rápidos",
            style = MaterialTheme.typography.titleLarge,
            fontWeight = FontWeight.Bold
        )
        
        Spacer(modifier = Modifier.height(8.dp))
        
        Row(
            modifier = Modifier.fillMaxWidth(),
            horizontalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            AccesoRapidoCard(
                modifier = Modifier.weight(1f),
                titulo = "Recompensas",
                icono = Icons.Default.CardGiftcard,
                onClick = onNavigateToRecompensas
            )
            
            AccesoRapidoCard(
                modifier = Modifier.weight(1f),
                titulo = "Historial",
                icono = Icons.Default.History,
                onClick = onNavigateToHistorial
            )
            
            AccesoRapidoCard(
                modifier = Modifier.weight(1f),
                titulo = "Mis Entregas",
                icono = Icons.Default.List,
                onClick = onNavigateToEntregas
            )
        }
    }
}

/**
 * Tarjeta de acceso rápido.
 */
@Composable
private fun AccesoRapidoCard(
    titulo: String,
    icono: androidx.compose.ui.graphics.vector.ImageVector,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    Card(
        onClick = onClick,
        modifier = modifier
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Icon(
                imageVector = icono,
                contentDescription = null,
                modifier = Modifier.size(32.dp),
                tint = MaterialTheme.colorScheme.primary
            )
            
            Spacer(modifier = Modifier.height(8.dp))
            
            Text(
                text = titulo,
                style = MaterialTheme.typography.bodyMedium,
                fontWeight = FontWeight.Medium
            )
        }
    }
}

/**
 * Tarjeta que muestra una entrega.
 */
@Composable
private fun TarjetaEntrega(entrega: Entrega) {
    Card(
        modifier = Modifier.fillMaxWidth()
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            // Icono del material
            Surface(
                shape = MaterialTheme.shapes.medium,
                color = MaterialTheme.colorScheme.secondaryContainer,
                modifier = Modifier.size(48.dp)
            ) {
                Box(contentAlignment = Alignment.Center) {
                    Text(
                        text = entrega.tipoMaterial.getIcono(),
                        style = MaterialTheme.typography.headlineSmall
                    )
                }
            }
            
            Spacer(modifier = Modifier.width(12.dp))
            
            // Información
            Column(modifier = Modifier.weight(1f)) {
                Text(
                    text = entrega.tipoMaterial.display,
                    style = MaterialTheme.typography.titleMedium,
                    fontWeight = FontWeight.Bold
                )
                
                Text(
                    text = "${entrega.pesoKg} kg • ${entrega.puntosGenerados} pts",
                    style = MaterialTheme.typography.bodyMedium
                )
                
                Text(
                    text = entrega.fechaEntrega?.toRelativeTime() ?: "Ahora",
                    style = MaterialTheme.typography.bodySmall,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
            }
            
            // Badge de estado
            Surface(
                shape = MaterialTheme.shapes.small,
                color = entrega.estado.getColor().copy(alpha = 0.2f)
            ) {
                Text(
                    text = when (entrega.estado) {
                        EstadoEntrega.PENDIENTE -> "Pendiente"
                        EstadoEntrega.APROBADA -> "Aprobada"
                        EstadoEntrega.RECHAZADA -> "Rechazada"
                    },
                    modifier = Modifier.padding(horizontal = 8.dp, vertical = 4.dp),
                    style = MaterialTheme.typography.labelSmall,
                    color = entrega.estado.getColor()
                )
            }
        }
    }
}

/**
 * Mensaje cuando no hay datos.
 */
@Composable
private fun EmptyStateMessage(
    message: String,
    actionText: String,
    onAction: () -> Unit
) {
    Card(
        modifier = Modifier.fillMaxWidth()
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(32.dp),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Icon(
                imageVector = Icons.Default.Recycling,
                contentDescription = null,
                modifier = Modifier.size(64.dp),
                tint = MaterialTheme.colorScheme.onSurfaceVariant
            )
            
            Spacer(modifier = Modifier.height(16.dp))
            
            Text(
                text = message,
                style = MaterialTheme.typography.bodyLarge,
                color = MaterialTheme.colorScheme.onSurfaceVariant
            )
            
            Spacer(modifier = Modifier.height(16.dp))
            
            TextButton(onClick = onAction) {
                Text(actionText)
            }
        }
    }
}

```
11.4 EntregaScreen (Nueva Entrega)

📁 ui/screens/entrega/EntregaScreen.kt

```kotlin

package mx.edu.utng.reciclaDH.ui.screens.entrega

import android.net.Uri
import androidx.activity.compose.rememberLauncherForActivityResult
import androidx.activity.result.contract.ActivityResultContracts
import androidx.compose.foundation.Image
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.unit.dp
import androidx.hilt.navigation.compose.hiltViewModel
import coil.compose.rememberAsyncImagePainter
import mx.edu.utng.reciclaDH.data.model.TipoMaterial
import mx.edu.utng.reciclaDH.ui.components.*
import mx.edu.utng.reciclaDH.util.Resource
import mx.edu.utng.reciclaDH.util.formatPoints

/**
 * Pantalla para registrar una nueva entrega de material reciclable.
 */
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun EntregaScreen(
    onNavigateBack: () -> Unit,
    viewModel: EntregaViewModel = hiltViewModel()
) {
    val tipoMaterial by viewModel.tipoMaterial.collectAsState()
    val pesoText by viewModel.pesoText.collectAsState()
    val imagenUri by viewModel.imagenUri.collectAsState()
val comentarios by viewModel.comentarios.collectAsState()
val puntosEstimados by viewModel.puntosEstimados.collectAsState()
val crearState by viewModel.crearState.collectAsState()
val errorMessage by viewModel.errorMessage.collectAsState()

var showTipoMaterialDialog by remember { mutableStateOf(false) }

// Launcher para seleccionar imagen de galería
val imagePickerLauncher = rememberLauncherForActivityResult(
    contract = ActivityResultContracts.GetContent()
) { uri: Uri? ->
    viewModel.onImagenChange(uri)
}

// Manejar resultado de creación
LaunchedEffect(crearState) {
    when (crearState) {
        is Resource.Success -> {
            viewModel.limpiarFormulario()
            onNavigateBack()
        }
        else -> {}
    }
}

// Diálogo de carga
if (crearState is Resource.Loading) {
    LoadingDialog(message = "Registrando entrega...")
}

Scaffold(
    topBar = {
        TopAppBar(
            title = { Text("Nueva Entrega") },
            navigationIcon = {
                IconButton(onClick = onNavigateBack) {
                    Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                }
            }
        )
    }
) { paddingValues ->
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(paddingValues)
            .verticalScroll(rememberScrollState())
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        // Instrucciones
        Card(
            colors = CardDefaults.cardColors(
                containerColor = MaterialTheme.colorScheme.primaryContainer
            )
        ) {
            Row(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(16.dp),
                verticalAlignment = Alignment.CenterVertically
            ) {
                Icon(
                    imageVector = Icons.Default.Info,
                    contentDescription = null,
                    tint = MaterialTheme.colorScheme.primary
                )
                
                Spacer(modifier = Modifier.width(12.dp))
                
                Text(
                    text = "Registra tu material reciclable y acumula puntos. Un operador validará tu entrega.",
                    style = MaterialTheme.typography.bodyMedium
                )
            }
        }
        
        // Selector de tipo de material
        Text(
            text = "Tipo de Material",
            style = MaterialTheme.typography.titleMedium,
            fontWeight = FontWeight.Bold
        )
        
        OutlinedCard(
            onClick = { showTipoMaterialDialog = true },
            modifier = Modifier.fillMaxWidth()
        ) {
            Row(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(16.dp),
                verticalAlignment = Alignment.CenterVertically
            ) {
                Text(
                    text = tipoMaterial.getIcono(),
                    style = MaterialTheme.typography.headlineMedium
                )
                
                Spacer(modifier = Modifier.width(16.dp))
                
                Column(modifier = Modifier.weight(1f)) {
                    Text(
                        text = tipoMaterial.display,
                        style = MaterialTheme.typography.titleMedium,
                        fontWeight = FontWeight.Bold
                    )
                    Text(
                        text = "${tipoMaterial.puntosPorKg} puntos por kg",
                        style = MaterialTheme.typography.bodyMedium,
                        color = MaterialTheme.colorScheme.onSurfaceVariant
                    )
                }
                
                Icon(
                    imageVector = Icons.Default.ArrowDropDown,
                    contentDescription = null
                )
            }
        }
        
        // Campo de peso
        Text(
            text = "Peso",
            style = MaterialTheme.typography.titleMedium,
            fontWeight = FontWeight.Bold
        )
        
        ReciclaTextField(
            value = pesoText,
            onValueChange = { viewModel.onPesoChange(it) },
            label = "Peso en kilogramos",
            keyboardType = KeyboardType.Decimal,
            placeholder = "Ej: 2.5",
            leadingIcon = {
                Icon(Icons.Default.Scale, contentDescription = null)
            },
            trailingIcon = {
                Text(
                    text = "kg",
                    modifier = Modifier.padding(end = 12.dp),
                    style = MaterialTheme.typography.bodyLarge
                )
            },
            isError = errorMessage != null && pesoText.isNotBlank(),
            errorMessage = errorMessage
        )
        
        // Puntos estimados
        if (puntosEstimados > 0) {
            Card(
                colors = CardDefaults.cardColors(
                    containerColor = MaterialTheme.colorScheme.tertiaryContainer
                )
            ) {
                Row(
                    modifier = Modifier
                        .fillMaxWidth()
                        .padding(16.dp),
                    verticalAlignment = Alignment.CenterVertically
                ) {
                    Icon(
                        imageVector = Icons.Default.Stars,
                        contentDescription = null,
                        tint = MaterialTheme.colorScheme.tertiary,
                        modifier = Modifier.size(32.dp)
                    )
                    
                    Spacer(modifier = Modifier.width(12.dp))
                    
                    Column {
                        Text(
                            text = "Puntos estimados",
                            style = MaterialTheme.typography.bodyMedium
                        )
                        Text(
                            text = puntosEstimados.formatPoints(),
                            style = MaterialTheme.typography.titleLarge,
                            fontWeight = FontWeight.Bold,
                            color = MaterialTheme.colorScheme.tertiary
                        )
                    }
                }
            }
        }
        
        // Foto
        Text(
            text = "Fotografía (Opcional)",
            style = MaterialTheme.typography.titleMedium,
            fontWeight = FontWeight.Bold
        )
        
        if (imagenUri != null) {
            // Mostrar imagen seleccionada
            Card(modifier = Modifier.fillMaxWidth()) {
                Box {
                    Image(
                        painter = rememberAsyncImagePainter(imagenUri),
                        contentDescription = "Foto de la entrega",
                        modifier = Modifier
                            .fillMaxWidth()
                            .height(200.dp),
                        contentScale = ContentScale.Crop
                    )
                    
                    // Botón para eliminar imagen
                    IconButton(
                        onClick = { viewModel.onImagenChange(null) },
                        modifier = Modifier
                            .align(Alignment.TopEnd)
                            .padding(8.dp)
                    ) {
                        Surface(
                            shape = MaterialTheme.shapes.small,
                            color = MaterialTheme.colorScheme.surface.copy(alpha = 0.8f)
                        ) {
                            Icon(
                                imageVector = Icons.Default.Close,
                                contentDescription = "Eliminar foto",
                                modifier = Modifier.padding(4.dp)
                            )
                        }
                    }
                }
            }
        } else {
            // Botón para seleccionar imagen
            OutlinedCard(
                onClick = { imagePickerLauncher.launch("image/*") },
                modifier = Modifier.fillMaxWidth()
            ) {
                Column(
                    modifier = Modifier
                        .fillMaxWidth()
                        .padding(32.dp),
                    horizontalAlignment = Alignment.CenterHorizontally
                ) {
                    Icon(
                        imageVector = Icons.Default.CameraAlt,
                        contentDescription = null,
                        modifier = Modifier.size(48.dp),
                        tint = MaterialTheme.colorScheme.primary
                    )
                    
                    Spacer(modifier = Modifier.height(8.dp))
                    
                    Text(
                        text = "Toca para agregar foto",
                        style = MaterialTheme.typography.bodyMedium,
                        color = MaterialTheme.colorScheme.primary
                    )
                }
            }
        }
        
        // Comentarios
        Text(
            text = "Comentarios (Opcional)",
            style = MaterialTheme.typography.titleMedium,
            fontWeight = FontWeight.Bold
        )
        
        OutlinedTextField(
            value = comentarios,
            onValueChange = { viewModel.onComentariosChange(it) },
            modifier = Modifier
                .fillMaxWidth()
                .height(120.dp),
            placeholder = { Text("Agrega información adicional sobre tu entrega...") },
            maxLines = 5,
            shape = MaterialTheme.shapes.medium
        )
        
        Spacer(modifier = Modifier.height(8.dp))
        
        // Botón de enviar
        ReciclaButton(
            text = "Registrar Entrega",
            onClick = { viewModel.crearEntrega() },
            isLoading = crearState is Resource.Loading,
            enabled = pesoText.isNotBlank()
        )
        
        Spacer(modifier = Modifier.height(16.dp))
    }
    }
    // Diálogo de selección de material
    if (showTipoMaterialDialog) {
    AlertDialog(
        onDismissRequest = { showTipoMaterialDialog = false },
        title = { Text("Selecciona el tipo de material") },
        text = {
            Column(
                verticalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                TipoMaterial.values().forEach { tipo ->
                    Card(
                        onClick = {
                            viewModel.onTipoMaterialChange(tipo)
                            showTipoMaterialDialog = false
                        },
                        modifier = Modifier.fillMaxWidth()
                    ) {
                        Row(
                            modifier = Modifier
                                .fillMaxWidth()
                                .padding(16.dp),
                            verticalAlignment = Alignment.CenterVertically
                        ) {
                            Text(
                                text = tipo.getIcono(),
                                style = MaterialTheme.typography.headlineSmall
                            )
                            
                            Spacer(modifier = Modifier.width(16.dp))
                            
                            Column(modifier = Modifier.weight(1f)) {
                                Text(
                                    text = tipo.display,
                                    style = MaterialTheme.typography.titleSmall,
                                    fontWeight = FontWeight.Bold
                                )
                                Text(
                                    text = "${tipo.puntosPorKg} pts/kg",
                                    style = MaterialTheme.typography.bodySmall,
                                    color = MaterialTheme.colorScheme.onSurfaceVariant
                                )
                            }
                            
                            if (tipo == tipoMaterial) {
                                Icon(
                                    imageVector = Icons.Default.CheckCircle,
                                    contentDescription = null,
                                    tint = MaterialTheme.colorScheme.primary
                                )
                            }
                        }
                    }
                }
            }
        },
        confirmButton = {
            TextButton(onClick = { showTipoMaterialDialog = false }) {
                Text("Cerrar")
            }
        }
    )
    }
    }

```
---

### 11.5 RecompensasScreen (Lista de Recompensas)

📁 **ui/screens/recompensas/RecompensasScreen.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.screens.recompensas

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.hilt.navigation.compose.hiltViewModel
import coil.compose.AsyncImage
import mx.edu.utng.reciclaDH.data.model.Recompensa
import mx.edu.utng.reciclaDH.ui.components.*
import mx.edu.utng.reciclaDH.util.Resource
import mx.edu.utng.reciclaDH.util.formatPoints
import mx.edu.utng.reciclaDH.util.toMoney

/**
 * Pantalla que muestra las recompensas disponibles para canje.
 */
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun RecompensasScreen(
    onNavigateBack: () -> Unit,
    onNavigateToDetalle: (String) -> Unit,
    viewModel: RecompensasViewModel = hiltViewModel()
) {
    val recompensasState by viewModel.recompensas.collectAsState()
    val puntosUsuario by viewModel.puntosUsuario.collectAsState()
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Recompensas Disponibles") },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                }
            )
        }
    ) { paddingValues ->
        
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
        ) {
            // Tarjeta de puntos disponibles
            Card(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(16.dp),
                colors = CardDefaults.cardColors(
                    containerColor = MaterialTheme.colorScheme.primaryContainer
                )
            ) {
                Row(
                    modifier = Modifier
                        .fillMaxWidth()
                        .padding(16.dp),
                    verticalAlignment = Alignment.CenterVertically
                ) {
                    Icon(
                        imageVector = Icons.Default.Stars,
                        contentDescription = null,
                        modifier = Modifier.size(32.dp),
                        tint = MaterialTheme.colorScheme.primary
                    )
                    
                    Spacer(modifier = Modifier.width(12.dp))
                    
                    Column {
                        Text(
                            text = "Tus puntos disponibles",
                            style = MaterialTheme.typography.bodyMedium
                        )
                        Text(
                            text = puntosUsuario.formatPoints(),
                            style = MaterialTheme.typography.titleLarge,
                            fontWeight = FontWeight.Bold,
                            color = MaterialTheme.colorScheme.primary
                        )
                    }
                }
            }
            
            // Lista de recompensas
            when (recompensasState) {
                is Resource.Loading -> {
                    Box(
                        modifier = Modifier.fillMaxSize(),
                        contentAlignment = Alignment.Center
                    ) {
                        CircularProgressIndicator()
                    }
                }
                
                is Resource.Success -> {
                    val recompensas = (recompensasState as Resource.Success).data ?: emptyList()
                    
                    if (recompensas.isEmpty()) {
                        Box(
                            modifier = Modifier.fillMaxSize(),
                            contentAlignment = Alignment.Center
                        ) {
                            Column(
                                horizontalAlignment = Alignment.CenterHorizontally,
                                modifier = Modifier.padding(32.dp)
                            ) {
                                Icon(
                                    imageVector = Icons.Default.CardGiftcard,
                                    contentDescription = null,
                                    modifier = Modifier.size(64.dp),
                                    tint = MaterialTheme.colorScheme.onSurfaceVariant
                                )
                                
                                Spacer(modifier = Modifier.height(16.dp))
                                
                                Text(
                                    text = "No hay recompensas disponibles",
                                    style = MaterialTheme.typography.bodyLarge,
                                    color = MaterialTheme.colorScheme.onSurfaceVariant
                                )
                            }
                        }
                    } else {
                        LazyColumn(
                            contentPadding = PaddingValues(16.dp),
                            verticalArrangement = Arrangement.spacedBy(12.dp)
                        ) {
                            items(recompensas) { recompensa ->
                                TarjetaRecompensa(
                                    recompensa = recompensa,
                                    puntosUsuario = puntosUsuario,
                                    onClick = { onNavigateToDetalle(recompensa.id) }
                                )
                            }
                        }
                    }
                }
                
                is Resource.Error -> {
                    ErrorScreen(
                        message = (recompensasState as Resource.Error).message 
                            ?: "Error al cargar recompensas",
                        onRetry = { viewModel.recargar() }
                    )
                }
                
                null -> {}
            }
        }
    }
}

/**
 * Tarjeta de recompensa individual.
 */
@Composable
private fun TarjetaRecompensa(
    recompensa: Recompensa,
    puntosUsuario: Int,
    onClick: () -> Unit
) {
    val puedeCanCanjear = puntosUsuario >= recompensa.costoEnPuntos
    
    Card(
        onClick = onClick,
        modifier = Modifier.fillMaxWidth()
    ) {
        Column {
            // Imagen de la recompensa
            if (recompensa.imagenUrl != null) {
                AsyncImage(
                    model = recompensa.imagenUrl,
                    contentDescription = recompensa.titulo,
                    modifier = Modifier
                        .fillMaxWidth()
                        .height(180.dp),
                    contentScale = ContentScale.Crop
                )
            } else {
                // Placeholder si no hay imagen
                Box(
                    modifier = Modifier
                        .fillMaxWidth()
                        .height(180.dp)
                        .padding(32.dp),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        text = recompensa.categoria.icono,
                        style = MaterialTheme.typography.displayLarge
                    )
                }
            }
            
            Column(
                modifier = Modifier.padding(16.dp)
            ) {
                // Categoría
                Surface(
                    shape = MaterialTheme.shapes.small,
                    color = MaterialTheme.colorScheme.secondaryContainer
                ) {
                    Text(
                        text = "${recompensa.categoria.icono} ${recompensa.categoria.display}",
                        modifier = Modifier.padding(horizontal = 8.dp, vertical = 4.dp),
                        style = MaterialTheme.typography.labelSmall
                    )
                }
                
                Spacer(modifier = Modifier.height(8.dp))
                
                // Título
                Text(
                    text = recompensa.titulo,
                    style = MaterialTheme.typography.titleLarge,
                    fontWeight = FontWeight.Bold
                )
                
                Spacer(modifier = Modifier.height(4.dp))
                
                // Descripción
                Text(
                    text = recompensa.descripcion,
                    style = MaterialTheme.typography.bodyMedium,
                    maxLines = 2,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
                
                Spacer(modifier = Modifier.height(12.dp))
                
                // Valor y costo
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.SpaceBetween,
                    verticalAlignment = Alignment.CenterVertically
                ) {
                    Column {
                        Text(
                            text = "Valor: ${recompensa.valorMonetario.toMoney()}",
                            style = MaterialTheme.typography.bodySmall,
                            color = MaterialTheme.colorScheme.onSurfaceVariant
                        )
                        
                        Text(
                            text = recompensa.costoEnPuntos.formatPoints(),
                            style = MaterialTheme.typography.titleMedium,
                            fontWeight = FontWeight.Bold,
                            color = if (puedeCanCanjear) {
                                MaterialTheme.colorScheme.primary
                            } else {
                                MaterialTheme.colorScheme.error
                            }
                        )
                    }
                    
                    // Badge de disponibilidad
                    if (recompensa.cantidadDisponible > 0 && 
                        recompensa.cantidadDisponible != Recompensa.STOCK_ILIMITADO) {
                        Surface(
                            shape = MaterialTheme.shapes.small,
                            color = if (recompensa.cantidadDisponible <= 5) {
                                MaterialTheme.colorScheme.errorContainer
                            } else {
                                MaterialTheme.colorScheme.tertiaryContainer
                            }
                        ) {
                            Text(
                                text = "Quedan ${recompensa.cantidadDisponible}",
                                modifier = Modifier.padding(horizontal = 8.dp, vertical = 4.dp),
                                style = MaterialTheme.typography.labelSmall
                            )
                        }
                    }
                }
                
                // Indicador si no tiene suficientes puntos
                if (!puedeCanCanjear) {
                    Spacer(modifier = Modifier.height(8.dp))
                    
                    Row(
                        verticalAlignment = Alignment.CenterVertically
                    ) {
                        Icon(
                            imageVector = Icons.Default.Info,
                            contentDescription = null,
                            modifier = Modifier.size(16.dp),
                            tint = MaterialTheme.colorScheme.error
                        )
                        
                        Spacer(modifier = Modifier.width(4.dp))
                        
                        Text(
                            text = "Te faltan ${(recompensa.costoEnPuntos - puntosUsuario).formatPoints()}",
                            style = MaterialTheme.typography.bodySmall,
                            color = MaterialTheme.colorScheme.error
                        )
                    }
                }
            }
        }
    }
}
```

---

### 11.6 RecompensasViewModel

📁 **ui/screens/recompensas/RecompensasViewModel.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.screens.recompensas

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.flow.*
import kotlinx.coroutines.launch
import mx.edu.utng.reciclaDH.data.model.Recompensa
import mx.edu.utng.reciclaDH.data.repository.AuthRepository
import mx.edu.utng.reciclaDH.data.repository.RecompensaRepository
import mx.edu.utng.reciclaDH.data.repository.UsuarioRepository
import mx.edu.utng.reciclaDH.util.Resource
import javax.inject.Inject

@HiltViewModel
class RecompensasViewModel @Inject constructor(
    private val authRepository: AuthRepository,
    private val recompensaRepository: RecompensaRepository,
    private val usuarioRepository: UsuarioRepository
) : ViewModel() {
    
    private val userId = authRepository.currentUserId ?: ""
    
    /**
     * Lista de recompensas disponibles.
     */
    val recompensas: StateFlow<Resource<List<Recompensa>>> = recompensaRepository
        .obtenerRecompensasActivas()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = Resource.Loading()
        )
    
    /**
     * Puntos del usuario actual.
     */
    val puntosUsuario: StateFlow<Int> = usuarioRepository
        .obtenerUsuarioFlow(userId)
        .map { resource ->
            when (resource) {
                is Resource.Success -> resource.data?.puntos ?: 0
                else -> 0
            }
        }
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = 0
        )
    
    fun recargar() {
        // Los flows se recargan automáticamente
        viewModelScope.launch {
            // Forzar recarga si es necesario
        }
    }
}
```

---

## **PARTE 12: NAVEGACIÓN (Navigation Graph)**

### 12.1 Definición de Rutas

📁 **ui/navigation/Screen.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.navigation

/**
 * Sealed class que define todas las rutas de navegación.
 * 
 * Usar sealed class en vez de Strings previene errores de tipeo
 * y facilita el refactoring.
 */
sealed class Screen(val route: String) {
    
    // Autenticación
    object Login : Screen("login")
    object Register : Screen("register")
    
    // Principal
    object Home : Screen("home")
    object Perfil : Screen("perfil")
    
    // Entregas
    object NuevaEntrega : Screen("nueva_entrega")
    object MisEntregas : Screen("mis_entregas")
    object DetalleEntrega : Screen("detalle_entrega/{entregaId}") {
        fun createRoute(entregaId: String) = "detalle_entrega/$entregaId"
    }
    
    // Recompensas
    object Recompensas : Screen("recompensas")
    object DetalleRecompensa : Screen("detalle_recompensa/{recompensaId}") {
        fun createRoute(recompensaId: String) = "detalle_recompensa/$recompensaId"
    }
    
    // Canjes
    object MisCanjes : Screen("mis_canjes")
    object Historial : Screen("historial")
    
    // Admin/Operador
    object ValidarEntregas : Screen("validar_entregas")
    object GestionRecompensas : Screen("gestion_recompensas")
    object GestionCanjes : Screen("gestion_canjes")
}
```

---

### 12.2 Navigation Graph

📁 **ui/navigation/NavGraph.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.navigation

import androidx.compose.runtime.Composable
import androidx.compose.runtime.LaunchedEffect
import androidx.navigation.NavHostController
import androidx.navigation.NavType
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.navArgument
import com.google.firebase.auth.FirebaseAuth
import mx.edu.utng.reciclaDH.ui.screens.auth.LoginScreen
import mx.edu.utng.reciclaDH.ui.screens.auth.RegisterScreen
import mx.edu.utng.reciclaDH.ui.screens.home.HomeScreen
import mx.edu.utng.reciclaDH.ui.screens.entrega.EntregaScreen
import mx.edu.utng.reciclaDH.ui.screens.recompensas.RecompensasScreen

/**
 * Grafo de navegación principal de la aplicación.
 * 
 * Define todas las pantallas y las transiciones entre ellas.
 */
@Composable
fun NavGraph(
    navController: NavHostController,
    auth: FirebaseAuth
) {
    // Determinar pantalla inicial según estado de autenticación
    val startDestination = if (auth.currentUser != null) {
        Screen.Home.route
    } else {
        Screen.Login.route
    }
    
    NavHost(
        navController = navController,
        startDestination = startDestination
    ) {
        
        // ========== AUTENTICACIÓN ==========
        
        composable(Screen.Login.route) {
            LoginScreen(
                onNavigateToRegister = {
                    navController.navigate(Screen.Register.route)
                },
                onNavigateToHome = {
                    navController.navigate(Screen.Home.route) {
                        popUpTo(Screen.Login.route) { inclusive = true }
                    }
                }
            )
        }
        
        composable(Screen.Register.route) {
            RegisterScreen(
                onNavigateToLogin = {
                    navController.popBackStack()
                },
                onNavigateToHome = {
                    navController.navigate(Screen.Home.route) {
                        popUpTo(Screen.Login.route) { inclusive = true }
                    }
                }
            )
        }
        
        // ========== HOME ==========
        
        composable(Screen.Home.route) {
            // Verificar autenticación
            LaunchedEffect(Unit) {
                if (auth.currentUser == null) {
                    navController.navigate(Screen.Login.route) {
                        popUpTo(0) { inclusive = true }
                    }
                }
            }
            
            HomeScreen(
                onNavigateToPerfil = {
                    navController.navigate(Screen.Perfil.route)
                },
                onNavigateToEntregas = {
                    navController.navigate(Screen.MisEntregas.route)
                },
                onNavigateToRecompensas = {
                    navController.navigate(Screen.Recompensas.route)
                },
                onNavigateToNuevaEntrega = {
                    navController.navigate(Screen.NuevaEntrega.route)
                },
                onNavigateToHistorial = {
                    navController.navigate(Screen.Historial.route)
                },
                onLogout = {
                    navController.navigate(Screen.Login.route) {
                        popUpTo(0) { inclusive = true }
                    }
                }
            )
        }
        
        // ========== ENTREGAS ==========
        
        composable(Screen.NuevaEntrega.route) {
            EntregaScreen(
                onNavigateBack = {
                    navController.popBackStack()
                }
            )
        }
        
        composable(Screen.MisEntregas.route) {
            // TODO: Implementar MisEntregasScreen
            // Por ahora, pantalla placeholder
            androidx.compose.material3.Text("Mis Entregas - En construcción")
        }
        
        composable(
            route = Screen.DetalleEntrega.route,
            arguments = listOf(
                navArgument("entregaId") { type = NavType.StringType }
            )
        ) { backStackEntry ->
            val entregaId = backStackEntry.arguments?.getString("entregaId") ?: ""
            // TODO: Implementar DetalleEntregaScreen
            androidx.compose.material3.Text("Detalle Entrega: $entregaId")
        }
        
        // ==========

Continuar

RECOMPENSAS ==========

    composable(Screen.Recompensas.route) {
        RecompensasScreen(
            onNavigateBack = {
                navController.popBackStack()
            },
            onNavigateToDetalle = { recompensaId ->
                navController.navigate(Screen.DetalleRecompensa.createRoute(recompensaId))
            }
        )
    }
    
    composable(
        route = Screen.DetalleRecompensa.route,
        arguments = listOf(
            navArgument("recompensaId") { type = NavType.StringType }
        )
    ) { backStackEntry ->
        val recompensaId = backStackEntry.arguments?.getString("recompensaId") ?: ""
        // TODO: Implementar DetalleRecompensaScreen
        androidx.compose.material3.Text("Detalle Recompensa: $recompensaId")
    }
    
    // ========== PERFIL ==========
    
    composable(Screen.Perfil.route) {
        // TODO: Implementar PerfilScreen
        androidx.compose.material3.Text("Perfil - En construcción")
    }
    
    // ========== HISTORIAL Y CANJES ==========
    
    composable(Screen.Historial.route) {
        // TODO: Implementar HistorialScreen
        androidx.compose.material3.Text("Historial - En construcción")
    }
    
    composable(Screen.MisCanjes.route) {
        // TODO: Implementar MisCanjesScreen
        androidx.compose.material3.Text("Mis Canjes - En construcción")
    }
}

}


---

## **PARTE 13: ACTIVITY PRINCIPAL**

### 13.1 MainActivity

📁 **MainActivity.kt**
```kotlin
package mx.edu.utng.reciclaDH

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.ui.Modifier
import androidx.navigation.compose.rememberNavController
import com.google.firebase.auth.FirebaseAuth
import dagger.hilt.android.AndroidEntryPoint
import mx.edu.utng.reciclaDH.ui.navigation.NavGraph
import mx.edu.utng.reciclaDH.ui.theme.ReciclaDolorFIRSTesTheme
import javax.inject.Inject

/**
 * Activity principal de la aplicación.
 * 
 * @AndroidEntryPoint permite que Hilt inyecte dependencias en esta Activity.
 */
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    
    @Inject
    lateinit var auth: FirebaseAuth
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        setContent {
            ReciclaDolorFIRSTesTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    val navController = rememberNavController()
                    
                    NavGraph(
                        navController = navController,
                        auth = auth
                    )
                }
            }
        }
    }
}
```

---

## **PARTE 14: REGLAS DE SEGURIDAD DE FIREBASE**

### 14.1 Firestore Security Rules

**Explicación para estudiantes:**

Las reglas de seguridad son como "guardias de seguridad" en Firebase. Deciden quién puede leer o escribir datos. Sin estas reglas, cualquiera podría robar o modificar los datos de la app.

**Conceptos clave:**
- `read`: Permite leer datos
- `write`: Permite crear, actualizar o eliminar datos
- `request.auth`: Información del usuario autenticado
- `resource.data`: Datos que ya existen en Firestore
- `request.resource.data`: Datos que se están intentando escribir

📄 **firestore.rules**
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // ========== FUNCIONES AUXILIARES ==========
    
    // Verifica si el usuario está autenticado
    function isSignedIn() {
      return request.auth != null;
    }
    
    // Verifica si el usuario es el dueño del documento
    function isOwner(userId) {
      return isSignedIn() && request.auth.uid == userId;
    }
    
    // Obtiene el rol del usuario desde su documento
    function getUserRole() {
      return get(/databases/$(database)/documents/users/$(request.auth.uid)).data.rol;
    }
    
    // Verifica si el usuario es admin
    function isAdmin() {
      return isSignedIn() && getUserRole() == 'admin';
    }
    
    // Verifica si el usuario es operador o admin
    function isOperadorOrAdmin() {
      return isSignedIn() && getUserRole() in ['operador', 'admin'];
    }
    
    // Verifica si el usuario está activo
    function isActive() {
      return isSignedIn() && 
             get(/databases/$(database)/documents/users/$(request.auth.uid)).data.activo == true;
    }
    
    // ========== COLECCIÓN: USERS ==========
    
    match /users/{userId} {
      // Cualquier usuario autenticado puede leer su propio perfil
      allow read: if isOwner(userId);
      
      // Solo el usuario puede crear su propio perfil (durante registro)
      allow create: if isOwner(userId) && 
                       request.resource.data.rol == 'ciudadano' &&
                       request.resource.data.puntos == 0 &&
                       request.resource.data.activo == true;
      
      // El usuario puede actualizar su perfil (excepto rol, puntos y activo)
      allow update: if isOwner(userId) && 
                       request.resource.data.rol == resource.data.rol &&
                       request.resource.data.puntos == resource.data.puntos &&
                       request.resource.data.activo == resource.data.activo;
      
      // Solo admins pueden cambiar rol o estado activo
      allow update: if isAdmin();
      
      // Solo admins pueden eliminar usuarios
      allow delete: if isAdmin();
    }
    
    // ========== COLECCIÓN: DELIVERIES (Entregas) ==========
    
    match /deliveries/{deliveryId} {
      // Usuarios pueden leer sus propias entregas
      allow read: if isActive() && isOwner(resource.data.usuarioId);
      
      // Operadores y admins pueden leer todas las entregas
      allow read: if isOperadorOrAdmin();
      
      // Usuarios pueden crear sus propias entregas
      allow create: if isActive() && 
                       isOwner(request.resource.data.usuarioId) &&
                       request.resource.data.estado == 'PENDIENTE' &&
                       request.resource.data.puntosGenerados >= 0 &&
                       request.resource.data.pesoKg > 0;
      
      // Usuarios pueden eliminar solo sus entregas PENDIENTES
      allow delete: if isActive() && 
                       isOwner(resource.data.usuarioId) &&
                       resource.data.estado == 'PENDIENTE';
      
      // Solo operadores/admins pueden validar (actualizar estado)
      allow update: if isOperadorOrAdmin() && 
                       // Solo puede cambiar estos campos
                       request.resource.data.diff(resource.data).affectedKeys()
                         .hasOnly(['estado', 'validadoPor', 'fechaValidacion', 'motivoRechazo']);
    }
    
    // ========== COLECCIÓN: REWARDS (Recompensas) ==========
    
    match /rewards/{rewardId} {
      // Todos los usuarios activos pueden ver recompensas activas
      allow read: if isActive();
      
      // Solo admins pueden crear, actualizar o eliminar recompensas
      allow create, update, delete: if isAdmin();
    }
    
    // ========== COLECCIÓN: REDEMPTIONS (Canjes) ==========
    
    match /redemptions/{redemptionId} {
      // Usuarios pueden ver sus propios canjes
      allow read: if isActive() && isOwner(resource.data.usuarioId);
      
      // Admins pueden ver todos los canjes
      allow read: if isAdmin();
      
      // Usuarios pueden crear canjes (solicitar recompensa)
      allow create: if isActive() && 
                       isOwner(request.resource.data.usuarioId) &&
                       request.resource.data.estado == 'SOLICITADO' &&
                       // Verificar que el usuario tenga suficientes puntos
                       get(/databases/$(database)/documents/users/$(request.auth.uid)).data.puntos 
                         >= request.resource.data.puntosUtilizados;
      
      // Usuarios pueden cancelar sus canjes (solo si están en SOLICITADO)
      allow update: if isActive() && 
                       isOwner(resource.data.usuarioId) &&
                       resource.data.estado == 'SOLICITADO' &&
                       request.resource.data.estado == 'CANCELADO';
      
      // Admins pueden actualizar el estado del canje
      allow update: if isAdmin();
      
      // Solo admins pueden eliminar canjes
      allow delete: if isAdmin();
    }
    
    // ========== REGLA POR DEFECTO ==========
    // Denegar acceso a cualquier otra colección no especificada
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

---

### 14.2 Storage Security Rules

**Explicación para estudiantes:**

Storage guarda las fotos. Estas reglas protegen que:
1. Solo usuarios autenticados suban fotos
2. Las fotos no sean muy grandes (máx 5MB)
3. Solo se suban imágenes (no virus ni otros archivos)

📄 **storage.rules**
```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    
    // ========== FUNCIONES AUXILIARES ==========
    
    function isSignedIn() {
      return request.auth != null;
    }
    
    function isOwner(userId) {
      return request.auth.uid == userId;
    }
    
    // Valida que sea una imagen
    function isImage() {
      return request.resource.contentType.matches('image/.*');
    }
    
    // Valida el tamaño (máximo 5MB)
    function isValidSize() {
      return request.resource.size < 5 * 1024 * 1024; // 5MB
    }
    
    // ========== FOTOS DE PERFIL ==========
    
    match /profiles/{userId}/{fileName} {
      // Cualquiera puede ver fotos de perfil
      allow read: if true;
      
      // Solo el dueño puede subir/actualizar su foto de perfil
      allow write: if isSignedIn() && 
                      isOwner(userId) && 
                      isImage() && 
                      isValidSize();
      
      // Solo el dueño puede eliminar su foto
      allow delete: if isSignedIn() && isOwner(userId);
    }
    
    // ========== FOTOS DE ENTREGAS ==========
    
    match /deliveries/{fileName} {
      // Usuarios autenticados pueden ver fotos de entregas
      allow read: if isSignedIn();
      
      // Usuarios autenticados pueden subir fotos de entregas
      allow write: if isSignedIn() && 
                      isImage() && 
                      isValidSize();
      
      // Los usuarios pueden eliminar fotos (cuando eliminan una entrega)
      allow delete: if isSignedIn();
    }
    
    // ========== FOTOS DE RECOMPENSAS ==========
    
    match /rewards/{fileName} {
      // Cualquiera puede ver fotos de recompensas
      allow read: if true;
      
      // Solo admins pueden subir fotos de recompensas
      // (Verificación de admin debe hacerse en el backend)
      allow write: if isSignedIn() && 
                      isImage() && 
                      isValidSize();
      
      allow delete: if isSignedIn();
    }
    
    // ========== COMPROBANTES DE CANJES ==========
    
    match /receipts/{fileName} {
      // Solo usuarios autenticados pueden ver comprobantes
      allow read: if isSignedIn();
      
      // Solo admins pueden subir comprobantes
      allow write: if isSignedIn() && 
                      isImage() && 
                      isValidSize();
    }
    
    // ========== REGLA POR DEFECTO ==========
    match /{allPaths=**} {
      allow read, write: if false;
    }
  }
}
```

---

## **PARTE 15: TEMA Y DISEÑO (Theme)**

### 15.1 Colores

📁 **ui/theme/Color.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.theme

import androidx.compose.ui.graphics.Color

// ========== COLORES PRIMARIOS (Verde Reciclaje) ==========
val Verde50 = Color(0xFFE8F5E9)
val Verde100 = Color(0xFFC8E6C9)
val Verde200 = Color(0xFFA5D6A7)
val Verde300 = Color(0xFF81C784)
val Verde400 = Color(0xFF66BB6A)
val Verde500 = Color(0xFF4CAF50) // Principal
val Verde600 = Color(0xFF43A047)
val Verde700 = Color(0xFF388E3C)
val Verde800 = Color(0xFF2E7D32)
val Verde900 = Color(0xFF1B5E20)

// ========== COLORES SECUNDARIOS (Azul Agua) ==========
val Azul50 = Color(0xFFE1F5FE)
val Azul100 = Color(0xFFB3E5FC)
val Azul200 = Color(0xFF81D4FA)
val Azul300 = Color(0xFF4FC3F7)
val Azul400 = Color(0xFF29B6F6)
val Azul500 = Color(0xFF03A9F4) // Secundario
val Azul600 = Color(0xFF039BE5)
val Azul700 = Color(0xFF0288D1)
val Azul800 = Color(0xFF0277BD)
val Azul900 = Color(0xFF01579B)

// ========== COLORES TERCIARIOS (Amarillo Energía) ==========
val Amarillo50 = Color(0xFFFFFDE7)
val Amarillo100 = Color(0xFFFFF9C4)
val Amarillo200 = Color(0xFFFFF59D)
val Amarillo300 = Color(0xFFFFF176)
val Amarillo400 = Color(0xFFFFEE58)
val Amarillo500 = Color(0xFFFFEB3B) // Terciario
val Amarillo600 = Color(0xFFFDD835)
val Amarillo700 = Color(0xFFFBC02D)
val Amarillo800 = Color(0xFFF9A825)
val Amarillo900 = Color(0xFFF57F17)

// ========== COLORES DE ESTADO ==========
val ErrorRojo = Color(0xFFD32F2F)
val WarningNaranja = Color(0xFFFFA726)
val SuccessVerde = Color(0xFF66BB6A)
val InfoAzul = Color(0xFF42A5F5)

// ========== COLORES NEUTROS ==========
val Gris50 = Color(0xFFFAFAFA)
val Gris100 = Color(0xFFF5F5F5)
val Gris200 = Color(0xFFEEEEEE)
val Gris300 = Color(0xFFE0E0E0)
val Gris400 = Color(0xFFBDBDBD)
val Gris500 = Color(0xFF9E9E9E)
val Gris600 = Color(0xFF757575)
val Gris700 = Color(0xFF616161)
val Gris800 = Color(0xFF424242)
val Gris900 = Color(0xFF212121)
```

---

### 15.2 Tipografía

📁 **ui/theme/Type.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.theme

import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

/**
 * Tipografía de Material Design 3.
 * 
 * Define los estilos de texto que se usan en toda la app.
 * Mantener consistencia visual es clave para una buena UX.
 */
val Typography = Typography(
    // Display - Títulos muy grandes (pantalla de bienvenida)
    displayLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Bold,
        fontSize = 57.sp,
        lineHeight = 64.sp
    ),
    displayMedium = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Bold,
        fontSize = 45.sp,
        lineHeight = 52.sp
    ),
    displaySmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Bold,
        fontSize = 36.sp,
        lineHeight = 44.sp
    ),
    
    // Headline - Títulos de secciones
    headlineLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Bold,
        fontSize = 32.sp,
        lineHeight = 40.sp
    ),
    headlineMedium = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.SemiBold,
        fontSize = 28.sp,
        lineHeight = 36.sp
    ),
    headlineSmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.SemiBold,
        fontSize = 24.sp,
        lineHeight = 32.sp
    ),
    
    // Title - Títulos de tarjetas y diálogos
    titleLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.SemiBold,
        fontSize = 22.sp,
        lineHeight = 28.sp
    ),
    titleMedium = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Medium,
        fontSize = 16.sp,
        lineHeight = 24.sp
    ),
    titleSmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Medium,
        fontSize = 14.sp,
        lineHeight = 20.sp
    ),
    
    // Body - Texto del cuerpo
    bodyLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 16.sp,
        lineHeight = 24.sp
    ),
    bodyMedium = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 14.sp,
        lineHeight = 20.sp
    ),
    bodySmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 12.sp,
        lineHeight = 16.sp
    ),
    
    // Label - Texto de botones y etiquetas
    labelLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Medium,
        fontSize = 14.sp,
        lineHeight = 20.sp
    ),
    labelMedium = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Medium,
        fontSize = 12.sp,
        lineHeight = 16.sp
    ),
    labelSmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Medium,
        fontSize = 11.sp,
        lineHeight = 16.sp
    )
)
```

---

### 15.3 Tema Principal

📁 **ui/theme/Theme.kt**
```kotlin
package mx.edu.utng.reciclaDH.ui.theme

import android.app.Activity
import android.os.Build
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.runtime.SideEffect
import androidx.compose.ui.graphics.toArgb
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.platform.LocalView
import androidx.core.view.WindowCompat

/**
 * Esquema de colores para modo claro.
 */
private val LightColorScheme = lightColorScheme(
    // Primarios (Verde)
    primary = Verde500,
    onPrimary = Color.White,
    primaryContainer = Verde100,
    onPrimaryContainer = Verde900,
    
    // Secundarios (Azul)
    secondary = Azul500,
    onSecondary = Color.White,
    secondaryContainer = Azul100,
    onSecondaryContainer = Azul900,
    
    // Terciarios (Amarillo)
    tertiary = Amarillo600,
    onTertiary = Gris900,
    tertiaryContainer = Amarillo100,
    onTertiaryContainer = Amarillo900,
    
    // Error
    error = ErrorRojo,
    onError = Color.White,
    errorContainer = Color(0xFFFFDAD6),
    onErrorContainer = Color(0xFF410002),
    
    // Background
    background = Gris50,
    onBackground = Gris900,
    
    // Surface
    surface = Color.White,
    onSurface = Gris900,
    surfaceVariant = Gris100,
    onSurfaceVariant = Gris700,
    
    // Outline
    outline = Gris400,
    outlineVariant = Gris300
)

/**
 * Esquema de colores para modo oscuro.
 */
private val DarkColorScheme = darkColorScheme(
    // Primarios (Verde)
    primary = Verde300,
    onPrimary = Verde900,
    primaryContainer = Verde700,
    onPrimaryContainer = Verde100,
    
    // Secundarios (Azul)
    secondary = Azul300,
    onSecondary = Azul900,
    secondaryContainer = Azul700,
    onSecondaryContainer = Azul100,
    
    // Terciarios (Amarillo)
    tertiary = Amarillo400,
    onTertiary = Amarillo900,
    tertiaryContainer = Amarillo700,
    onTertiaryContainer = Amarillo100,
    
    // Error
    error = Color(0xFFFFB4AB),
    onError = Color(0xFF690005),
    errorContainer = Color(0xFF93000A),
    onErrorContainer = Color(0xFFFFDAD6),
    
    // Background
    background = Gris900,
    onBackground = Gris50,
    
    // Surface
    surface = Gris800,
    onSurface = Gris50,
    surfaceVariant = Gris700,
    onSurfaceVariant = Gris300,
    
    // Outline
    outline = Gris600,
    outlineVariant = Gris700
)

/**
 * Tema principal de la aplicación.
 * 
 * @param darkTheme Si debe usar tema oscuro
 * @param dynamicColor Si debe usar colores dinámicos del sistema (Android 12+)
 * @param content Contenido de la app
 */
@Composable
fun ReciclaDolorFIRSTesTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        // Colores dinámicos en Android 12+
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) 
            else dynamicLightColorScheme(context)
        }
        // Tema oscuro
        darkTheme -> DarkColorScheme
        // Tema claro
        else -> LightColorScheme
    }
    
    val view = LocalView.current
    if (!view.isInEditMode) {
        SideEffect {
            val window = (view.context as Activity).window
            window.statusBarColor = colorScheme.primary.toArgb()
            WindowCompat.getInsetsController(window, view).isAppearanceLightStatusBars = !darkTheme
        }
    }

    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        content = content
    )
}
```

---

## **PARTE 16: EJERCICIOS PRÁCTICOS PARA ESTUDIANTES**

### 📚 Ejercicio 1: Modificar Puntos por Kilogramo

**Objetivo:** Aprender a modificar modelos de datos y ver cómo afecta toda la app.

**Tarea:**
1. Cambiar los puntos de PET de 10 a 15 puntos por kg
2. Agregar un nuevo tipo de material: "Textil" con 7 puntos por kg
3. Probar que los cambios se reflejan en:
   - El formulario de nueva entrega
   - El cálculo de puntos estimados
   - Las entregas guardadas

**Pistas:**
- Archivo a modificar: `data/model/Entrega.kt`
- Buscar el enum `TipoMaterial`
- Agregar nuevo valor al enum
- Recompilar la app

---

### 📚 Ejercicio 2: Agregar Validación de Foto Obligatoria

**Objetivo:** Practicar validaciones en ViewModels.

**Tarea:**
1. Hacer que la foto sea obligatoria al crear una entrega
2. Mostrar mensaje de error si intentan enviar sin foto
3. Deshabilitar el botón "Registrar Entrega" si no hay foto

**Pistas:**
- Archivo: `ui/screens/entrega/EntregaViewModel.kt`
- Modificar función `validarFormulario()`
- Agregar verificación de `_imagenUri.value != null`
- En `EntregaScreen.kt`, modificar `enabled` del botón

**Solución propuesta:**
```kotlin
// En EntregaViewModel.kt
private fun validarFormulario(): Boolean {
    val peso = _pesoText.value.toDoubleOrNull()
    
    when {
        _imagenUri.value == null -> {
            _errorMessage.value = "La foto es obligatoria"
            return false
        }
        peso == null || peso <= 0 -> {
            _errorMessage.value = "Ingresa un peso válido"
            return false
        }
        // ... resto de validaciones
    }
    
    return true
}

// En EntregaScreen.kt
ReciclaButton(
    text = "Registrar Entrega",
    onClick = { viewModel.crearEntrega() },
    isLoading = crearState is Resource.Loading,
    enabled = pesoText.isNotBlank() && imagenUri != null // ← AGREGAR ESTA CONDICIÓN
)
```

---

### 📚 Ejercicio 3: Crear Pantalla de Ranking

**Objetivo:** Practicar consumo de repositorios y crear pantallas completas.

**Tarea:**
1. Crear una pantalla que muestre el ranking de usuarios con más puntos
2. Mostrar top 10 usuarios
3. Resaltar el usuario actual en la lista
4. Agregar medallas para los 3 primeros lugares

**Estructura sugerida:**
```kotlin
// RankingViewModel.kt
@HiltViewModel
class RankingViewModel @Inject constructor(
    private val usuarioRepository: UsuarioRepository,
    private val authRepository: AuthRepository
) : ViewModel() {
    
    private val _ranking = MutableStateFlow<Resource<List<Usuario>>>(Resource.Loading())
    val ranking: StateFlow<Resource<List<Usuario>>> = _ranking.asStateFlow()
    
    private val currentUserId = authRepository.currentUserId
    
    init {
        cargarRanking()
    }
    
    private fun cargarRanking() {
        viewModelScope.launch {
            _ranking.value = Resource.Loading()
            _ranking.value = usuarioRepository.obtenerRankingUsuarios(10)
        }
    }
    
    fun esUsuarioActual(userId: String): Boolean {
        return userId == currentUserId
    }
}

// RankingScreen.kt
@Composable
fun RankingScreen(
    onNavigateBack: () -> Unit,
    viewModel: RankingViewModel = hiltViewModel()
) {
    val rankingState by viewModel.ranking.collectAsState()
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Ranking de Recicladores") },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                }
            )
        }
    ) { paddingValues ->
        when (rankingState) {
            is Resource.Loading -> {
                // Mostrar indicador de carga
            }
            is Resource.Success -> {
                val usuarios = (rankingState as Resource.Success).data ?: emptyList()
                LazyColumn(
                    modifier = Modifier.padding(paddingValues),
                    contentPadding = PaddingValues(16.dp),
                    verticalArrangement = Arrangement.spacedBy(8.dp)
                ) {
                    itemsIndexed(usuarios) { index, usuario ->
                        TarjetaRanking(
                            posicion = index + 1,
                            usuario = usuario,
                            esUsuarioActual = viewModel.esUsuarioActual(usuario.id)
                        )
                    }
                }
            }
            is Resource.Error -> {
                // Mostrar error
            }
            null -> {}
        }
    }
}

@Composable
private fun TarjetaRanking(
    posicion: Int,
    usuario: Usuario,
    esUsuarioActual: Boolean
) {
    Card(
        modifier = Modifier.fillMaxWidth(),
        colors = CardDefaults.cardColors(
            containerColor = if (esUsuarioActual) {
                MaterialTheme.colorScheme.primaryContainer
            } else {
                MaterialTheme.colorScheme.surface
            }
        )
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            // Medalla para top 3
            Text(
                text = when (posicion) {
                    1 -> "🥇"
                    2 -> "🥈"
                    3 -> "

Continuar

🥉"
else -> "#$posicion"
},
style = MaterialTheme.typography.headlineSmall,
fontWeight = FontWeight.Bold,
modifier = Modifier.width(60.dp)
)

        Spacer(modifier = Modifier.width(16.dp))
        
        Column(modifier = Modifier.weight(1f)) {
            Text(
                text = usuario.nombre,
                style = MaterialTheme.typography.titleMedium,
                fontWeight = if (esUsuarioActual) FontWeight.Bold else FontWeight.Normal
            )
            
            if (esUsuarioActual) {
                Text(
                    text = "¡Eres tú!",
                    style = MaterialTheme.typography.bodySmall,
                    color = MaterialTheme.colorScheme.primary
                )
            }
        }
        
        Text(
            text = usuario.puntos.formatPoints(),
            style = MaterialTheme.typography.titleLarge,
            fontWeight = FontWeight.Bold,
            color = MaterialTheme.colorScheme.primary
        )
    }
}

}


---

### 📚 Ejercicio 4: Implementar Filtros en Historial

**Objetivo:** Practicar manejo de estado y filtrado de datos.

**Tarea:**
1. Crear pantalla de historial de entregas
2. Agregar filtros por:
   - Estado (Todas, Pendientes, Aprobadas, Rechazadas)
   - Tipo de material
   - Rango de fechas
3. Implementar búsqueda por peso

**Estructura sugerida:**
````kotlin
// HistorialViewModel.kt
@HiltViewModel
class HistorialViewModel @Inject constructor(
    private val authRepository: AuthRepository,
    private val entregaRepository: EntregaRepository
) : ViewModel() {
    
    private val userId = authRepository.currentUserId ?: ""
    
    // Estado de filtros
    private val _filtroEstado = MutableStateFlow<EstadoEntrega?>(null)
    val filtroEstado = _filtroEstado.asStateFlow()
    
    private val _filtroMaterial = MutableStateFlow<TipoMaterial?>(null)
    val filtroMaterial = _filtroMaterial.asStateFlow()
    
    private val _searchQuery = MutableStateFlow("")
    val searchQuery = _searchQuery.asStateFlow()
    
    // Entregas sin filtrar
    private val todasLasEntregas = entregaRepository
        .obtenerEntregasUsuario(userId)
    
    // Entregas filtradas
    val entregasFiltradas: StateFlow<Resource<List<Entrega>>> = combine(
        todasLasEntregas,
        _filtroEstado,
        _filtroMaterial,
        _searchQuery
    ) { entregas, estado, material, query ->
        when (entregas) {
            is Resource.Success -> {
                var lista = entregas.data ?: emptyList()
                
                // Filtrar por estado
                if (estado != null) {
                    lista = lista.filter { it.estado == estado }
                }
                
                // Filtrar por material
                if (material != null) {
                    lista = lista.filter { it.tipoMaterial == material }
                }
                
                // Filtrar por búsqueda (peso)
                if (query.isNotBlank()) {
                    val pesoQuery = query.toDoubleOrNull()
                    if (pesoQuery != null) {
                        lista = lista.filter { it.pesoKg >= pesoQuery }
                    }
                }
                
                Resource.Success(lista)
            }
            is Resource.Error -> entregas
            is Resource.Loading -> entregas
        }
    }.stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5000),
        initialValue = Resource.Loading()
    )
    
    // Estadísticas basadas en filtros
    val estadisticasFiltradas: StateFlow<Map<String, Any>> = entregasFiltradas
        .map { resource ->
            when (resource) {
                is Resource.Success -> {
                    val entregas = resource.data ?: emptyList()
                    mapOf(
                        "total" to entregas.size,
                        "pesoTotal" to entregas.sumOf { it.pesoKg },
                        "puntosTotal" to entregas
                            .filter { it.estado == EstadoEntrega.APROBADA }
                            .sumOf { it.puntosGenerados }
                    )
                }
                else -> emptyMap()
            }
        }
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = emptyMap()
        )
    
    // Acciones
    fun setFiltroEstado(estado: EstadoEntrega?) {
        _filtroEstado.value = estado
    }
    
    fun setFiltroMaterial(material: TipoMaterial?) {
        _filtroMaterial.value = material
    }
    
    fun setSearchQuery(query: String) {
        _searchQuery.value = query
    }
    
    fun limpiarFiltros() {
        _filtroEstado.value = null
        _filtroMaterial.value = null
        _searchQuery.value = ""
    }
}
````

---

### 📚 Ejercicio 5: Sistema de Notificaciones

**Objetivo:** Integrar Cloud Messaging para notificaciones push.

**Tarea:**
1. Configurar Firebase Cloud Messaging
2. Enviar notificación cuando una entrega sea aprobada
3. Enviar notificación cuando un canje sea procesado
4. Mostrar badge de notificaciones en el app

**Pasos:**

**1. Agregar dependencia en `build.gradle.kts`:**
````kotlin
implementation("com.google.firebase:firebase-messaging-ktx")
````

**2. Crear servicio de mensajería:**
````kotlin
// MessagingService.kt
package mx.edu.utng.reciclaDH.services

import android.app.NotificationChannel
import android.app.NotificationManager
import android.app.PendingIntent
import android.content.Intent
import android.os.Build
import androidx.core.app.NotificationCompat
import com.google.firebase.messaging.FirebaseMessagingService
import com.google.firebase.messaging.RemoteMessage
import mx.edu.utng.reciclaDH.MainActivity
import mx.edu.utng.reciclaDH.R

class ReciclaMessagingService : FirebaseMessagingService() {
    
    override fun onMessageReceived(remoteMessage: RemoteMessage) {
        super.onMessageReceived(remoteMessage)
        
        // Manejar notificación
        remoteMessage.notification?.let {
            mostrarNotificacion(
                titulo = it.title ?: "ReciclaD Hidalgo",
                mensaje = it.body ?: ""
            )
        }
    }
    
    override fun onNewToken(token: String) {
        super.onNewToken(token)
        // Guardar token en Firestore para el usuario
        // TODO: Implementar guardado de token
    }
    
    private fun mostrarNotificacion(titulo: String, mensaje: String) {
        val channelId = "recicla_channel"
        
        // Crear canal de notificación (Android 8+)
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                channelId,
                "Notificaciones de Reciclaje",
                NotificationManager.IMPORTANCE_HIGH
            ).apply {
                description = "Notificaciones sobre tus entregas y canjes"
            }
            
            val notificationManager = getSystemService(NotificationManager::class.java)
            notificationManager.createNotificationChannel(channel)
        }
        
        // Intent para abrir la app
        val intent = Intent(this, MainActivity::class.java).apply {
            flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
        }
        
        val pendingIntent = PendingIntent.getActivity(
            this,
            0,
            intent,
            PendingIntent.FLAG_IMMUTABLE
        )
        
        // Construir notificación
        val notification = NotificationCompat.Builder(this, channelId)
            .setSmallIcon(R.drawable.ic_notification) // Crear este icono
            .setContentTitle(titulo)
            .setContentText(mensaje)
            .setPriority(NotificationCompat.PRIORITY_HIGH)
            .setContentIntent(pendingIntent)
            .setAutoCancel(true)
            .build()
        
        // Mostrar notificación
        val notificationManager = getSystemService(NotificationManager::class.java)
        notificationManager.notify(System.currentTimeMillis().toInt(), notification)
    }
}
````

**3. Registrar servicio en `AndroidManifest.xml`:**
````xml
<service
    android:name=".services.ReciclaMessagingService"
    android:exported="false">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
````

---

## **PARTE 17: BUENAS PRÁCTICAS Y CONSEJOS**

### 🎯 Buenas Prácticas de Arquitectura

**1. Separación de Responsabilidades**
````kotlin
// ❌ MAL: Todo en un solo lugar
@Composable
fun PantallaUsuario() {
    val firestore = FirebaseFirestore.getInstance()
    var nombre by remember { mutableStateOf("") }
    
    LaunchedEffect(Unit) {
        firestore.collection("users").document("123").get()
            .addOnSuccessListener { nombre = it.getString("nombre") ?: "" }
    }
    
    Text(nombre)
}

// ✅ BIEN: Separado en capas
// Repository
class UsuarioRepository {
    suspend fun obtenerUsuario(id: String): Usuario { ... }
}

// ViewModel
class UsuarioViewModel(private val repository: UsuarioRepository) : ViewModel() {
    val usuario = repository.obtenerUsuarioFlow(id)
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(), Resource.Loading())
}

// Screen
@Composable
fun PantallaUsuario(viewModel: UsuarioViewModel = hiltViewModel()) {
    val usuario by viewModel.usuario.collectAsState()
    // UI pura, sin lógica de negocio
}
````

**2. Manejo de Estados Consistente**
````kotlin
// ✅ Usar Resource para todos los estados
sealed class Resource<T> {
    class Loading<T> : Resource<T>()
    class Success<T>(val data: T) : Resource<T>()
    class Error<T>(val message: String) : Resource<T>()
}

// En UI, siempre manejar los 3 casos
when (state) {
    is Resource.Loading -> CircularProgressIndicator()
    is Resource.Success -> MostrarDatos(state.data)
    is Resource.Error -> MostrarError(state.message)
}
````

**3. Inmutabilidad de Datos**
````kotlin
// ❌ MAL: Modificar datos directamente
data class Usuario(var puntos: Int)

val usuario = Usuario(100)
usuario.puntos = 200 // ¡Peligroso! Dificulta rastrear cambios

// ✅ BIEN: Usar copy() para crear nuevas instancias
data class Usuario(val puntos: Int)

val usuario = Usuario(100)
val usuarioActualizado = usuario.copy(puntos = 200)
````

---

### 🔒 Seguridad

**1. Nunca confiar en el cliente**
````kotlin
// ❌ MAL: Validar solo en la app
fun crearEntrega(puntos: Int) {
    // Un usuario malicioso podría modificar este valor
    firestore.collection("deliveries").add(mapOf("puntos" to 999999))
}

// ✅ BIEN: Validar en el backend (Cloud Functions)
// functions/index.js
exports.validarEntrega = functions.firestore
    .document('deliveries/{deliveryId}')
    .onCreate((snap, context) => {
        const entrega = snap.data();
        const puntosCalculados = entrega.pesoKg * getTipoPuntos(entrega.tipo);
        
        // Verificar que los puntos sean correctos
        if (entrega.puntosGenerados !== puntosCalculados) {
            return snap.ref.update({ puntosGenerados: puntosCalculados });
        }
    });
````

**2. Proteger información sensible**
````kotlin
// ❌ MAL: Exponer información sensible
data class Usuario(
    val email: String,
    val password: String, // ¡NUNCA guardar contraseñas!
    val tarjetaCredito: String
)

// ✅ BIEN: Solo información necesaria
data class Usuario(
    val id: String,
    val nombre: String,
    val puntos: Int
    // Información sensible se maneja solo en backend
)
````

---

### ⚡ Optimización de Rendimiento

**1. Paginación de datos**
````kotlin
// Para listas largas, implementar paginación
fun obtenerEntregasPaginadas(limite: Int, ultimoDoc: DocumentSnapshot?) {
    var query = firestore.collection("deliveries")
        .orderBy("fechaEntrega", Query.Direction.DESCENDING)
        .limit(limite.toLong())
    
    if (ultimoDoc != null) {
        query = query.startAfter(ultimoDoc)
    }
    
    return query.get()
}
````

**2. Usar índices en Firestore**
````kotlin
// Si haces queries compuestas, crear índices
// Firebase te sugerirá los índices necesarios en los logs
firestore.collection("deliveries")
    .whereEqualTo("usuarioId", userId)
    .whereEqualTo("estado", "APROBADA")
    .orderBy("fechaEntrega", Query.Direction.DESCENDING)
    .get()
// Firestore sugerirá crear un índice compuesto
````

**3. Cachear datos offline**
````kotlin
// Habilitar persistencia de Firestore
FirebaseFirestore.getInstance().apply {
    firestoreSettings = FirebaseFirestoreSettings.Builder()
        .setPersistenceEnabled(true)
        .setCacheSizeBytes(FirebaseFirestoreSettings.CACHE_SIZE_UNLIMITED)
        .build()
}
````

---

### 🧪 Testing

**Ejemplo de test para ViewModel:**
````kotlin
// EntregaViewModelTest.kt
@ExperimentalCoroutinesTest
class EntregaViewModelTest {
    
    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()
    
    private lateinit var viewModel: EntregaViewModel
    private lateinit var fakeAuthRepository: FakeAuthRepository
    private lateinit var fakeEntregaRepository: FakeEntregaRepository
    
    @Before
    fun setup() {
        fakeAuthRepository = FakeAuthRepository()
        fakeEntregaRepository = FakeEntregaRepository()
        
        viewModel = EntregaViewModel(
            authRepository = fakeAuthRepository,
            entregaRepository = fakeEntregaRepository,
            usuarioRepository = FakeUsuarioRepository()
        )
    }
    
    @Test
    fun `calcular puntos correctamente`() = runTest {
        // Given
        viewModel.onTipoMaterialChange(TipoMaterial.PET) // 10 pts/kg
        viewModel.onPesoChange("2.5")
        
        // When
        advanceUntilIdle()
        
        // Then
        val puntosEstimados = viewModel.puntosEstimados.value
        assertEquals(25, puntosEstimados) // 2.5 kg * 10 pts/kg = 25 pts
    }
    
    @Test
    fun `validar peso mínimo`() = runTest {
        // Given
        viewModel.onPesoChange("0.05") // Menos del mínimo (0.1 kg)
        
        // When
        viewModel.crearEntrega()
        advanceUntilIdle()
        
        // Then
        val errorMessage = viewModel.errorMessage.value
        assertNotNull(errorMessage)
        assertTrue(errorMessage!!.contains("mínimo"))
    }
}
````

---

## **PARTE 18: RECURSOS ADICIONALES Y DOCUMENTACIÓN**

### 📖 Documentación Oficial Recomendada

**Para estudiantes, estos son los recursos IMPRESCINDIBLES:**

1. **Jetpack Compose:**
   - https://developer.android.com/jetpack/compose/tutorial
   - https://developer.android.com/courses/pathways/compose

2. **Firebase:**
   - https://firebase.google.com/docs/android/setup
   - https://firebase.google.com/docs/firestore/quickstart

3. **MVVM y Architecture Components:**
   - https://developer.android.com/topic/architecture
   - https://developer.android.com/topic/libraries/architecture/viewmodel

4. **Hilt (Inyección de Dependencias):**
   - https://developer.android.com/training/dependency-injection/hilt-android

---

### 🎓 Rúbrica de Evaluación Sugerida

Para evaluar el proyecto completo de los estudiantes:

| Criterio | Puntos | Descripción |
|----------|--------|-------------|
| **Arquitectura** | 20 | Correcta implementación de MVVM, Repository Pattern, separación de capas |
| **Funcionalidad** | 30 | Todas las funciones principales funcionan correctamente |
| **UI/UX** | 15 | Interfaz intuitiva, consistente, uso correcto de Material Design 3 |
| **Seguridad** | 15 | Reglas de Firestore correctas, validaciones implementadas |
| **Código Limpio** | 10 | Nombres descriptivos, comentarios apropiados, organización |
| **Manejo de Errores** | 10 | Casos límite manejados, mensajes de error claros |

---

### 🚀 Próximos Pasos y Mejoras

**Funcionalidades avanzadas que los estudiantes pueden agregar:**

1. **Gamificación:**
   - Logros y badges
   - Niveles de usuario (Bronce, Plata, Oro)
   - Retos semanales

2. **Social:**
   - Compartir logros en redes sociales
   - Equipos o grupos de reciclaje
   - Muro comunitario

3. **Analytics:**
   - Dashboard para admins
   - Gráficas de reciclaje por mes
   - Impacto ambiental calculado (CO2 ahorrado)

4. **Geolocalización:**
   - Mapa con centros de acopio cercanos
   - Ruta óptima para entregar material
   - Check-in en centros de acopio

5. **Inteligencia Artificial:**
   - Clasificación automática de material con ML Kit
   - Detección de peso por foto
   - Chatbot de ayuda

---

### 📝 Checklist Final del Proyecto

**Antes de entregar, verificar:**

- [ ] La app compila sin errores
- [ ] Firebase está correctamente configurado
- [ ] Las reglas de seguridad están implementadas
- [ ] Todos los formularios tienen validación
- [ ] Los errores se manejan adecuadamente
- [ ] La navegación funciona correctamente
- [ ] Las imágenes se suben y descargan correctamente
- [ ] Los puntos se calculan correctamente
- [ ] El tema es consistente en toda la app
- [ ] La app funciona sin internet (modo offline básico)
- [ ] El código está documentado con comentarios
- [ ] README.md está actualizado con instrucciones

---

### 📱 Estructura del README.md
````markdown
# ReciclaD Hidalgo - App de Reciclaje con Recompensas

## 📋 Descripción
Aplicación móvil que incentiva el reciclaje mediante un sistema de puntos canjeables por becas y apoyos municipales en Dolores Hidalgo, Guanajuato.

## 🎯 Características
- ✅ Registro y autenticación de usuarios
- ✅ Registro de entregas de material reciclable
- ✅ Sistema de puntos por reciclaje
- ✅ Catálogo de recompensas
- ✅ Historial de entregas y canjes
- ✅ Panel de validación para operadores

## 🛠️ Tecnologías Utilizadas
- **Lenguaje:** Kotlin
- **UI:** Jetpack Compose
- **Arquitectura:** MVVM + Repository Pattern
- **Inyección de Dependencias:** Hilt
- **Backend:** Firebase (Auth, Firestore, Storage)
- **Navegación:** Navigation Compose
- **Imágenes:** Coil

## 📦 Requisitos
- Android Studio Hedgehog o superior
- JDK 17
- Cuenta de Firebase
- Dispositivo Android con API 24+ (Android 7.0)

## 🚀 Instalación

1. Clonar el repositorio:
```bash
git clone https://github.com/tu-usuario/recicla-dolores.git
```

2. Abrir en Android Studio

3. Configurar Firebase:
   - Crear proyecto en Firebase Console
   - Descargar `google-services.json`
   - Colocarlo en `app/`

4. Sincronizar Gradle y compilar

## 📖 Uso

### Usuario Ciudadano:
1. Registrarse/Iniciar sesión
2. Registrar entrega de material
3. Esperar validación del operador
4. Acumular puntos
5. Canjear por recompensas

### Operador:
1. Revisar entregas pendientes
2. Aprobar/rechazar entregas
3. Gestionar validaciones

### Administrador:
1. Crear recompensas
2. Gestionar usuarios
3. Procesar canjes

## 👥 Autores
- [Tu Nombre] - Desarrollo principal
- [Profesor] - Asesoría técnica

## 📄 Licencia
Este proyecto es de código abierto bajo licencia MIT.
````

---

## **🎉 CONCLUSIÓN DEL MANUAL**

### Resumen de lo que hemos cubierto:

✅ **Fundamentos teóricos** explicados con analogías para estudiantes  
✅ **Arquitectura MVVM** completa con Repository Pattern  
✅ **Configuración de Firebase** paso a paso  
✅ **Modelos de datos** completos (Usuario, Entrega, Recompensa, Canje)  
✅ **Repositorios** con operaciones CRUD  
✅ **ViewModels** con lógica de negocio  
✅ **Pantallas UI** con Jetpack Compose  
✅ **Navegación** completa  
✅ **Componentes reutilizables**  
✅ **Reglas de seguridad** de Firebase  
✅ **Tema personalizado** con Material Design 3  
✅ **Ejercicios prácticos** para estudiantes  
✅ **Buenas prácticas** y optimizaciones  
✅ **Testing** básico  

### 🎓 Mensaje final para el docente:

Este manual está diseñado para que puedas:
1. **Enseñar paso a paso** a tus estudiantes
2. **Adaptarlo** a las necesidades específicas de tu curso
3. **Usarlo como base** para proyectos similares
4. **Evaluar** el progreso mediante los ejercicios propuestos

Los estudiantes aprenderán:
- Desarrollo Android moderno con Jetpack Compose
- Arquitectura de software profesional
- Integración con servicios en la nube
- Resolución de problemas reales
- Buenas prácticas de programación

### 📚 ¿Necesitas más?

Si necesitas alguna sección adicional, como:
- Implementación de Cloud Functions
- Testing más avanzado
- CI/CD con GitHub Actions
- Despliegue en Play Store
- Más pantallas específicas

¡Solo pídemelo y lo desarrollo con el mismo nivel de detalle!

Todas las pantallas del proyecto restante y codigos necesarios para su funcionamiento
PARTE 19: PANTALLAS RESTANTES Y CÓDIGO COMPLETO
19.1 PerfilScreen (Pantalla de Perfil)

📁 ui/screens/perfil/PerfilViewModel.kt

```kotlin

package mx.edu.utng.reciclaDH.ui.screens.perfil

import android.net.Uri
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.flow.*
import kotlinx.coroutines.launch
import mx.edu.utng.reciclaDH.data.model.Usuario
import mx.edu.utng.reciclaDH.data.repository.AuthRepository
import mx.edu.utng.reciclaDH.data.repository.UsuarioRepository
import mx.edu.utng.reciclaDH.util.Resource
import mx.edu.utng.reciclaDH.util.toTitleCase
import java.io.File
import javax.inject.Inject

/**
 * ViewModel para la pantalla de perfil del usuario.
 * 
 * Permite:
 * - Ver información del usuario
 * - Editar nombre, teléfono, dirección
 * - Cambiar foto de perfil
 * - Cambiar contraseña
 */
@HiltViewModel
class PerfilViewModel @Inject constructor(
    private val authRepository: AuthRepository,
    private val usuarioRepository: UsuarioRepository
) : ViewModel() {
    
    private val userId = authRepository.currentUserId ?: ""
    
    /**
     * Usuario actual con actualizaciones en tiempo real.
     */
    val usuario: StateFlow<Resource<Usuario>> = usuarioRepository
        .obtenerUsuarioFlow(userId)
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = Resource.Loading()
        )
    
    // ========== ESTADO DEL FORMULARIO ==========
    
    private val _modoEdicion = MutableStateFlow(false)
    val modoEdicion: StateFlow<Boolean> = _modoEdicion.asStateFlow()
    
    private val _nombre = MutableStateFlow("")
    val nombre: StateFlow<String> = _nombre.asStateFlow()
    
    private val _telefono = MutableStateFlow("")
    val telefono: StateFlow<String> = _telefono.asStateFlow()
    
    private val _direccion = MutableStateFlow("")
    val direccion: StateFlow<String> = _direccion.asStateFlow()
    
    private val _updateState = MutableStateFlow<Resource<Unit>?>(null)
    val updateState: StateFlow<Resource<Unit>?> = _updateState.asStateFlow()
    
    private val _uploadPhotoState = MutableStateFlow<Resource<String>?>(null)
    val uploadPhotoState: StateFlow<Resource<String>?> = _uploadPhotoState.asStateFlow()
    
    private val _errorMessage = MutableStateFlow<String?>(null)
    val errorMessage: StateFlow<String?> = _errorMessage.asStateFlow()
    
    init {
        cargarDatosUsuario()
    }
    
    /**
     * Carga los datos del usuario en el formulario.
     */
    private fun cargarDatosUsuario() {
        viewModelScope.launch {
            usuario.collect { resource ->
                if (resource is Resource.Success) {
                    resource.data?.let { user ->
                        _nombre.value = user.nombre
                        _telefono.value = user.telefono
                        _direccion.value = user.direccion
                    }
                }
            }
        }
    }
    
    // ========== ACCIONES ==========
    
    fun activarModoEdicion() {
        _modoEdicion.value = true
    }
    
    fun cancelarEdicion() {
        _modoEdicion.value = false
        cargarDatosUsuario() // Restaurar valores originales
        _errorMessage.value = null
    }
    
    fun onNombreChange(newNombre: String) {
        _nombre.value = newNombre
        _errorMessage.value = null
    }
    
    fun onTelefonoChange(newTelefono: String) {
        // Solo permitir números y limitar a 10 dígitos
        val filtrado = newTelefono.filter { it.isDigit() }.take(10)
        _telefono.value = filtrado
        _errorMessage.value = null
    }
    
    fun onDireccionChange(newDireccion: String) {
        _direccion.value = newDireccion
        _errorMessage.value = null
    }
    
    /**
     * Valida los campos del formulario.
     */
    private fun validarFormulario(): Boolean {
        when {
            _nombre.value.isBlank() -> {
                _errorMessage.value = "El nombre no puede estar vacío"
                return false
            }
            _nombre.value.length < 3 -> {
                _errorMessage.value = "El nombre debe tener al menos 3 caracteres"
                return false
            }
            _telefono.value.isBlank() -> {
                _errorMessage.value = "El teléfono no puede estar vacío"
                return false
            }
            _telefono.value.length != 10 -> {
                _errorMessage.value = "El teléfono debe tener 10 dígitos"
                return false
            }
        }
        return true
    }
    
    /**
     * Guarda los cambios del perfil.
     */
    fun guardarCambios() {
        if (!validarFormulario()) return
        
        viewModelScope.launch {
            _updateState.value = Resource.Loading()
            
            val updates = mapOf(
                "nombre" to _nombre.value.trim().toTitleCase(),
                "telefono" to _telefono.value,
                "direccion" to _direccion.value.trim()
            )
            
            val result = usuarioRepository.actualizarPerfil(userId, updates)
            _updateState.value = result
            
            if (result is Resource.Success) {
                _modoEdicion.value = false
            }
        }
    }
    
    /**
     * Sube una nueva foto de perfil.
     * 
     * @param imageFile Archivo de imagen a subir
     */
    fun subirFotoPerfil(imageFile: File) {
        viewModelScope.launch {
            _uploadPhotoState.value = Resource.Loading()
            val result = usuarioRepository.subirFotoPerfil(userId, imageFile)
            _uploadPhotoState.value = result
        }
    }
    
    /**
     * Cambia la contraseña del usuario.
     * 
     * @param nuevaPassword Nueva contraseña
     */
    fun cambiarPassword(nuevaPassword: String): Flow<Resource<Unit>> = flow {
        emit(Resource.Loading())
        
        if (nuevaPassword.length < 6) {
            emit(Resource.Error("La contraseña debe tener al menos 6 caracteres"))
            return@flow
        }
        
        val result = authRepository.actualizarPassword(nuevaPassword)
        emit(result)
    }
    
    fun limpiarEstados() {
        _updateState.value = null
        _uploadPhotoState.value = null
        _errorMessage.value = null
    }
}

📁 ui/screens/perfil/PerfilScreen.kt
kotlin

package mx.edu.utng.reciclaDH.ui.screens.perfil

import android.net.Uri
import androidx.activity.compose.rememberLauncherForActivityResult
import androidx.activity.result.contract.ActivityResultContracts
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.verticalScroll
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.unit.dp
import androidx.hilt.navigation.compose.hiltViewModel
import coil.compose.AsyncImage
import mx.edu.utng.reciclaDH.ui.components.*
import mx.edu.utng.reciclaDH.util.Resource
import mx.edu.utng.reciclaDH.util.formatPoints
import mx.edu.utng.reciclaDH.util.toFormattedString
import java.io.File

/**
 * Pantalla de perfil del usuario.
 */
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun PerfilScreen(
    onNavigateBack: () -> Unit,
    viewModel: PerfilViewModel = hiltViewModel()
) {
    val usuarioState by viewModel.usuario.collectAsState()
    val modoEdicion by viewModel.modoEdicion.collectAsState()
    val nombre by viewModel.nombre.collectAsState()
    val telefono by viewModel.telefono.collectAsState()
    val direccion by viewModel.direccion.collectAsState()
    val updateState by viewModel.updateState.collectAsState()
    val uploadPhotoState by viewModel.uploadPhotoState.collectAsState()
    val errorMessage by viewModel.errorMessage.collectAsState()
    
    var showPasswordDialog by remember { mutableStateOf(false) }
    var showConfirmDialog by remember { mutableStateOf(false) }
    
    // Launcher para seleccionar imagen
    val imagePickerLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.GetContent()
    ) { uri: Uri? ->
        uri?.let {
            // Convertir URI a File (simplificado, en producción usar un método más robusto)
            // TODO: Implementar conversión correcta de URI a File
        }
    }
    
    // Manejar resultado de actualización
    LaunchedEffect(updateState) {
        when (updateState) {
            is Resource.Success -> {
                viewModel.limpiarEstados()
            }
            else -> {}
        }
    }
    
    // Diálogo de carga
    if (updateState is Resource.Loading || uploadPhotoState is Resource.Loading) {
        LoadingDialog(message = "Guardando cambios...")
    }
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Mi Perfil") },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                },
                actions = {
                    if (!modoEdicion) {
                        IconButton(onClick = { viewModel.activarModoEdicion() }) {
                            Icon(Icons.Default.Edit, contentDescription = "Editar")
                        }
                    }
                }
            )
        }
    ) { paddingValues ->
        
        when (usuarioState) {
            is Resource.Loading -> {
                Box(
                    modifier = Modifier
                        .fillMaxSize()
                        .padding(paddingValues),
                    contentAlignment = Alignment.Center
                ) {
                    CircularProgressIndicator()
                }
            }
            
            is Resource.Success -> {
                val usuario = (usuarioState as Resource.Success).data!!
                
                Column(
                    modifier = Modifier
                        .fillMaxSize()
                        .padding(paddingValues)
                        .verticalScroll(rememberScrollState())
                        .padding(16.dp),
                    verticalArrangement = Arrangement.spacedBy(16.dp)
                ) {
                    // Foto de perfil y puntos
                    Card(
                        modifier = Modifier.fillMaxWidth(),
                        colors = CardDefaults.cardColors(
                            containerColor = MaterialTheme.colorScheme.primaryContainer
                        )
                    ) {
                        Column(
                            modifier = Modifier
                                .fillMaxWidth()
                                .padding(24.dp),
                            horizontalAlignment = Alignment.CenterHorizontally
                        ) {
                            // Foto de perfil
                            Box {
                                AsyncImage(
                                    model = usuario.fotoPerfilUrl ?: "https://via.placeholder.com/150",
                                    contentDescription = "Foto de perfil",
                                    modifier = Modifier
                                        .size(120.dp)
                                        .clip(CircleShape),
                                    contentScale = ContentScale.Crop
                                )
                                
                                // Botón para cambiar foto
                                if (!modoEdicion) {
                                    IconButton(
                                        onClick = { imagePickerLauncher.launch("image/*") },
                                        modifier = Modifier
                                            .align(Alignment.BottomEnd)
                                            .size(36.dp)
                                    ) {
                                        Surface(
                                            shape = CircleShape,
                                            color = MaterialTheme.colorScheme.primary
                                        ) {
                                            Icon(
                                                imageVector = Icons.Default.CameraAlt,
                                                contentDescription = "Cambiar foto",
                                                modifier = Modifier.padding(6.dp),
                                                tint = MaterialTheme.colorScheme.onPrimary
                                            )
                                        }
                                    }
                                }
                            }
                            
                            Spacer(modifier = Modifier.height(16.dp))
                            
                            // Puntos
                            Text(
                                text = usuario.puntos.formatPoints(),
                                style = MaterialTheme.typography.displaySmall,
                                fontWeight = FontWeight.Bold,
                                color = MaterialTheme.colorScheme.primary
                            )
                            
                            Text(
                                text = "Puntos acumulados",
                                style = MaterialTheme.typography.bodyMedium
                            )
                        }
                    }
                    
                    // Información del perfil
                    Text(
                        text = "Información Personal",
                        style = MaterialTheme.typography.titleLarge,
                        fontWeight = FontWeight.Bold
                    )
                    
                    // Nombre
                    if (modoEdicion) {
                        ReciclaTextField(
                            value = nombre,
                            onValueChange = { viewModel.onNombreChange(it) },
                            label = "Nombre completo",
                            leadingIcon = {
                                Icon(Icons.Default.Person, contentDescription = null)
                            }
                        )
                    } else {
                        InfoCard(
                            icono = Icons.Default.Person,
                            titulo = "Nombre",
                            valor = usuario.nombre
                        )
                    }
                    
                    // Email (no editable)
                    InfoCard(
                        icono = Icons.Default.Email,
                        titulo = "Correo electrónico",
                        valor = usuario.email
                    )
                    
                    // Teléfono
                    if (modoEdicion) {
                        ReciclaTextField(
                            value = telefono,
                            onValueChange = { viewModel.onTelefonoChange(it) },
                            label = "Teléfono",
                            keyboardType = KeyboardType.Phone,
                            leadingIcon = {
                                Icon(Icons.Default.Phone, contentDescription = null)
                            }
                        )
                    } else {
                        InfoCard(
                            icono = Icons.Default.Phone,
                            titulo = "Teléfono",
                            valor = usuario.telefono
                        )
                    }
                    
                    // Dirección
                    if (modoEdicion) {
                        ReciclaTextField(
                            value = direccion,
                            onValueChange = { viewModel.onDireccionChange(it) },
                            label = "Dirección",
                            leadingIcon = {
                                Icon(Icons.Default.Home, contentDescription = null)
                            },
                            maxLines = 3
                        )
                    } else {
                        InfoCard(
                            icono = Icons.Default.Home,
                            titulo = "Dirección",
                            valor = usuario.direccion.ifBlank { "No especificada" }
                        )
                    }
                    
                    // Información adicional
                    Divider()
                    
                    InfoCard(
                        icono = Icons.Default.Badge,
                        titulo = "Rol",
                        valor = when (usuario.rol) {
                            "ciudadano" -> "Ciudadano"
                            "operador" -> "Operador"
                            "admin" -> "Administrador"
                            else -> usuario.rol
                        }
                    )
                    
                    InfoCard(
                        icono = Icons.Default.CalendarToday,
                        titulo = "Miembro desde",
                        valor = usuario.fechaRegistro?.toFormattedString() ?: "N/A"
                    )
                    
                    // Mensaje de error
                    if (errorMessage != null) {
                        Text(
                            text = errorMessage!!,
                            color = MaterialTheme.colorScheme.error,
                            style = MaterialTheme.typography.bodySmall
                        )
                    }
                    
                    // Botones de acción
                    if (modoEdicion) {
                        Row(
                            modifier = Modifier.fillMaxWidth(),
                            horizontalArrangement = Arrangement.spacedBy(8.dp)
                        ) {
                            ReciclaOutlinedButton(
                                text = "Cancelar",
                                onClick = { viewModel.cancelarEdicion() },
                                modifier = Modifier.weight(1f)
                            )
                            
                            ReciclaButton(
                                text = "Guardar",
                                onClick = { viewModel.guardarCambios() },
                                modifier = Modifier.weight(1f),
                                isLoading = updateState is Resource.Loading
                            )
                        }
                    } else {
                        // Opciones adicionales
                        Divider()
                        
                        Text(
                            text = "Opciones",
                            style = MaterialTheme.typography.titleLarge,
                            fontWeight = FontWeight.Bold
                        )
                        
                        // Cambiar contraseña
                        OutlinedCard(
                            onClick = { showPasswordDialog = true },
                            modifier = Modifier.fillMaxWidth()
                        ) {
                            Row(
                                modifier = Modifier
                                    .fillMaxWidth()
                                    .padding(16.dp),
                                verticalAlignment = Alignment.CenterVertically
                            ) {
                                Icon(
                                    imageVector = Icons.Default.Lock,
                                    contentDescription = null,
                                    tint = MaterialTheme.colorScheme.primary
                                )
                                
                                Spacer(modifier = Modifier.width(16.dp))
                                
                                Text(
                                    text = "Cambiar contraseña",
                                    style = MaterialTheme.typography.titleMedium,
                                    modifier = Modifier.weight(1f)
                                )
                                
                                Icon(
                                    imageVector = Icons.Default.ChevronRight,
                                    contentDescription = null
                                )
                            }
                        }
                    }
                }
            }
            
            is Resource.Error -> {
                ErrorScreen(
                    message = (usuarioState as Resource.Error).message 
                        ?: "Error al cargar perfil",
                    onRetry = { /* Recargar */ }
                )
            }
            
            null -> {}
        }
    }
    
    // Diálogo de cambio de contraseña
    if (showPasswordDialog) {
        CambiarPasswordDialog(
            onDismiss = { showPasswordDialog = false },
            onConfirm = { nuevaPassword ->
                // Implementar cambio de contraseña
                showPasswordDialog = false
            }
        )
    }
}

/**
 * Tarjeta de información (no editable).
 */
@Composable
private fun InfoCard(
    icono: androidx.compose.ui.graphics.vector.ImageVector,
    titulo: String,
    valor: String
) {
    OutlinedCard(modifier = Modifier.fillMaxWidth()) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            Icon(
                imageVector = icono,
                contentDescription = null,
                tint = MaterialTheme.colorScheme.primary
            )
            
            Spacer(modifier = Modifier.width(16.dp))
            
            Column {
                Text(
                    text = titulo,
                    style = MaterialTheme.typography.labelMedium,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
                
                Text(
                    text = valor,
                    style = MaterialTheme.typography.bodyLarge,
                    fontWeight = FontWeight.Medium
                )
            }
        }
    }
}

/**
 * Diálogo para cambiar contraseña.
 */
@Composable
private fun CambiarPasswordDialog(
    onDismiss: () -> Unit,
    onConfirm: (String) -> Unit
) {
    var nuevaPassword by remember { mutableStateOf("") }
    var confirmarPassword by remember { mutableStateOf("") }
    var passwordVisible by remember { mutableStateOf(false) }
    var errorMessage by remember { mutableStateOf<String?>(null) }
    
    AlertDialog(
        onDismissRequest = onDismiss,
        title = { Text("Cambiar Contraseña") },
        text = {
            Column(
                verticalArrangement = Arrangement.spacedBy(12.dp)
            ) {
                ReciclaTextField(
                    value = nuevaPassword,
                    onValueChange = {
                        nuevaPassword = it
                        errorMessage = null
                    },
                    label = "Nueva contraseña",
                    keyboardType = KeyboardType.Password,
                    visualTransformation = if (passwordVisible) {
                        androidx.compose.ui.text.input.VisualTransformation.None
                    } else {
                        androidx.compose.ui.text.input.PasswordVisualTransformation()
                    },
                    trailingIcon = {
                        IconButton(onClick = { passwordVisible = !passwordVisible }) {
                            Icon(
                                imageVector = if (passwordVisible) {
                                    Icons.Default.Visibility
                                } else {
                                    Icons.Default.VisibilityOff
                                },
                                contentDescription = null
                            )
                        }
                    }
                )
                
                ReciclaTextField(
                    value = confirmarPassword,
                    onValueChange = {
                        confirmarPassword = it
                        errorMessage = null
                    },
                    label = "Confirmar contraseña",
                    keyboardType = KeyboardType.Password,
                    visualTransformation = if (passwordVisible) {
                        androidx.compose.ui.text.input.VisualTransformation.None
                    } else {
                        androidx.compose.ui.text.input.PasswordVisualTransformation()
                    }
                )
                
                if (errorMessage != null) {
                    Text(
                        text = errorMessage!!,
                        color = MaterialTheme.colorScheme.error,
                        style = MaterialTheme.typography.bodySmall
                    )
                }
            }
        },
        confirmButton = {
            TextButton(
                onClick = {
                    when {
                        nuevaPassword.length < 6 -> {
                            errorMessage = "La contraseña debe tener al menos 6 caracteres"
                        }
                        nuevaPassword != confirmarPassword -> {
                            errorMessage = "Las contraseñas no coinciden"
                        }
                        else -> {
                            onConfirm(nuevaPassword)
                        }
                    }
                }
            ) {
                Text("Cambiar")
            }
        },
        dismissButton = {
            TextButton(onClick = onDismiss) {
                Text("Cancelar")
            }
        }
    )
}

```

19.2 MisEntregasScreen (Historial de Entregas)

📁 ui/screens/entregas/MisEntregasViewModel.kt

```kotlin

package mx.edu.utng.reciclaDH.ui.screens.entregas

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.flow.*
import kotlinx.coroutines.launch
import mx.edu.utng.reciclaDH.data.model.Entrega
import mx.edu.utng.reciclaDH.data.model.EstadoEntrega
import mx.edu.utng.reciclaDH.data.model.TipoMaterial
import mx.edu.utng.reciclaDH.data.repository.AuthRepository
import mx.edu.utng.reciclaDH.data.repository.EntregaRepository
import mx.edu.utng.reciclaDH.util.Resource
import javax.inject.Inject

@HiltViewModel
class MisEntregasViewModel @Inject constructor(
    private val authRepository: AuthRepository,
    private val entregaRepository: EntregaRepository
) : ViewModel() {
    
    private val userId = authRepository.currentUserId ?: ""
    
    // ========== FILTROS ==========
    
    private val _filtroEstado = MutableStateFlow<EstadoEntrega?>(null)
    val filtroEstado = _filtroEstado.asStateFlow()
    
    private val _filtroMaterial = MutableStateFlow<TipoMaterial?>(null)
    val filtroMaterial = _filtroMaterial.asStateFlow()
    
    // ========== DATOS ==========
    
    private val todasLasEntregas = entregaRepository
        .obtenerEntregasUsuario(userId)
    
    /**
     * Entregas filtradas según los criterios seleccionados.
     */
    val entregas: StateFlow<Resource<List<Entrega>>> = combine(
        todasLasEntregas,
        _filtroEstado,
        _filtroMaterial
    ) { entregas, estado, material ->
        when (entregas) {
            is Resource.Success -> {
                var lista = entregas.data ?: emptyList()
                
                // Aplicar filtro de estado
                if (estado != null) {
                    lista = lista.filter { it.estado == estado }
                }
                
                // Aplicar filtro de material
                if (material != null) {
                    lista = lista.filter { it.tipoMaterial == material }
                }
                
                Resource.Success(lista)
            }
            is Resource.Error -> entregas
            is Resource.Loading -> entregas
        }
    }.stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(5000),
        initialValue = Resource.Loading()
    )
    
    /**
     * Estadísticas de las entregas filtradas.
     */
    val estadisticas: StateFlow<Map<String, Any>> = entregas
        .map { resource ->
            when (resource) {
                is Resource.Success -> {
                    val lista = resource.data ?: emptyList()
                    mapOf(
                        "total" to lista.size,
                        "pendientes" to lista.count { it.estado == EstadoEntrega.PENDIENTE },
                        "aprobadas" to lista.count { it.estado == EstadoEntrega.APROBADA },
                        "rechazadas" to lista.count { it.estado == EstadoEntrega.RECHAZADA },
                        "pesoTotal" to lista.sumOf { it.pesoKg },
                        "puntosTotal" to lista
                            .filter { it.estado == EstadoEntrega.APROBADA }
                            .sumOf { it.puntosGenerados }
                    )
                }
                else -> emptyMap()
            }
        }
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5000),
            initialValue = emptyMap()
        )
    
    // ========== ACCIONES ==========
    
    fun setFiltroEstado(estado: EstadoEntrega?) {
        _filtroEstado.value = estado
    }
    
    fun setFiltroMaterial(material: TipoMaterial?) {
        _filtroMaterial.value = material
    }
    
    fun limpiarFiltros() {
        _filtroEstado.value = null
        _filtroMaterial.value = null
    }
    
    /**
     * Elimina una entrega (solo si está pendiente).
     */
    suspend fun eliminarEntrega(entregaId: String): Resource<Unit> {
        return entregaRepository.eliminarEntrega(entregaId, userId)
    }
}

```
📁 ui/screens/entregas/MisEntregasScreen.kt

```kotlin

package mx.edu.utng.reciclaDH.ui.screens.entregas

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.hilt.navigation.compose.hiltViewModel
import mx.edu.utng.reciclaDH.data.model.Entrega
import mx.edu.utng.reciclaDH.data.model.EstadoEntrega
import mx.edu.utng.reciclaDH.data.model.TipoMaterial
import mx.edu.utng.reciclaDH.ui.components.*
import mx.edu.utng.reciclaDH.util.Resource
import mx.edu.utng.reciclaDH.util.formatPoints
import mx.edu.utng.reciclaDH.util.toFormattedDateTime
import kotlinx.coroutines.launch

/**
 * Pantalla que muestra todas las entregas del usuario.
 */
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun MisEntregasScreen(
    onNavigateBack: () -> Unit,
    onNavigateToDetalle: (String) -> Unit,
    viewModel: MisEntregasViewModel = hiltViewModel()
) {
    val entregasState by viewModel.entregas.collectAsState()
    val estadisticas by viewModel.estadisticas.collectAsState()
    val filtroEstado by viewModel.filtroEstado.collectAsState()
    val filtroMaterial by viewModel.filtroMaterial.collectAsState()
    
    var showFiltrosDialog by remember { mutableStateOf(false) }
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Mis Entregas") },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Volver")
                    }
                },
                actions = {
                    // Botón de filtros
