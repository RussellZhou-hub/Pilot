# Piccolo Engine


[跳转到中文](#table1)    

open main.cpp you can see there are 2 primary object(engine and ediotr) and some function call to start and run whole engine. This page will briefly record their basic functionality and purpose.

## engine->startEngine

The first step is to register the field variables and functions of the reflection class (setter getter, etc.)，
```c++
Reflection::TypeMetaRegister::Register();
```
Register() function actually encapsulate the registered interfaces of all classes to be reflected, such as
```c++
TypeWrappersRegister::CameraComponent();
    TypeWrappersRegister::Quaternion();
    TypeWrappersRegister::Transform();
```
In fact, TypeWrapperRegister is called TypeWrapperRegister_ XXX function respectively, which is defined in the c++ code file generated by the reflector, as follows:
```c++
void CameraComponent(){ TypeWrapperRegister_CameraComponent();}
```
The registered operation generally saves field functions through tuples (tuple is a set of fixed size values of different types, which is a generalized std:: pair), as follows:
```c++
void TypeWrapperRegister_CameraComponent(){
        FieldFunctionTuple* f_field_function_tuple_m_camera_res=new FieldFunctionTuple(
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::set_m_camera_res,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::get_m_camera_res,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::getClassName,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::getFieldName_m_camera_res,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::getFieldTypeName_m_camera_res,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::isArray_m_camera_res);
        REGISTER_FIELD_TO_MAP("CameraComponent", f_field_function_tuple_m_camera_res);
```
Start initialization of global variable 'g_runtime_global_context' after registration.
```c++
g_runtime_global_context.startSystems(config_file_path);
```
RuntimeGlobalContext is a global variable defined in the GlobalContext module. The main member variable is the smart pointer of each sub module manager.
```c++
        std::shared_ptr<LogSystem>       m_logger_system;
        std::shared_ptr<InputSystem>     m_input_system;
        std::shared_ptr<FileSystem>      m_file_system;
        //...
        std::shared_ptr<RenderSystem>    m_render_system;
        std::shared_ptr<ParticleManager> m_particle_manager;
```

## engine->initialize

It seems that this function has not been implemented yet. It is defined in engine.cpp as follows:
```c++
void PiccoloEngine::initialize() {}
```
## editor->initialize
The constructor of editor will be called here
```c++
Piccolo::PiccoloEditor* editor = new Piccolo::PiccoloEditor();
```
The constructor uses the registerEdtorTickComponent function to register the components that the editor needs to tick(TransformComponent and MeshComponent for now)
```c++
void registerEdtorTickComponent(std::string component_type_name)
{
    g_editor_tick_component_types.insert(component_type_name);
}
```
`g_editor_tick_component_types`is a global unordered_set defined in engine.cpp(In unordered_set The value of an element is also the key that uniquely identifies it. The key is immutable, so unordered_ Elements in the set cannot be modified in the container, but they can be inserted and deleted)
```c++
std::unordered_set<std::string> g_editor_tick_component_types {};
```
Then the editor is initialized. This function accepts an engine pointer variable parameter.
```c++
editor->initialize(engine);
```
The editor stores an engine pointer internally and assigns the value to the passed in engine pointer.
Initialize the window (encapsulate glfw), initialize Imgui, and call the initialization functions of the following members respectively.
```c++
g_editor_global_context.initialize(init_info);
//...
m_editor_ui->initialize(ui_init_info);
```

## editor->run
Call the tick function of editor context and the tick function of engine respectively.
```c++
while (true)
    {
        delta_time = m_engine_runtime->calculateDeltaTime();
        g_editor_global_context.m_scene_manager->tick(delta_time);
        g_editor_global_context.m_input_manager->tick(delta_time);
        if (!m_engine_runtime->tickOneFrame(delta_time))   return;
    }
```

## editor->clear
This step only uses delete keyword to release the following two members in g_editor_global_context:
```c++
class EditorSceneManager* m_scene_manager {nullptr};
class EditorInputManager* m_input_manager {nullptr};
```
## engine->clear
not implemented yet.
## engine->shutdownEngine
·Release all the resource pointed to by the intelligent pointer of the subsystem of g_runtime_global_context and set as a null pointer.
·Release all tuple related to reflection.

==================================================================Chinese
<a id="table1"></a>    <!--#要跳转的位置,id = 链接位置-->

打开main.cpp可以看到有两个主要对象（engine和ediotr）和一些函数调用来启动和运行整个引擎。本页将简要记录其基本功能和用途。

## engine->startEngine

第一步是注册反射类的域变量和函数(setter getter等)，
```c++
Reflection::TypeMetaRegister::Register();
```
Register()函数其实是封装了所有要反射的类的注册接口，比如
```c++
TypeWrappersRegister::CameraComponent();
    TypeWrappersRegister::Quaternion();
    TypeWrappersRegister::Transform();
```
等
实际上又是调用了TypeWrapperRegister_XXX函数，是由反射器生成的c++代码文件中定义的，如下：
```c++
void CameraComponent(){ TypeWrapperRegister_CameraComponent();}
```
注册的操作一般通过元组来保存域函数（ tuple是一个固定大小的不同类型值的集合，是泛化的std::pair），如下：
```c++
void TypeWrapperRegister_CameraComponent(){
        FieldFunctionTuple* f_field_function_tuple_m_camera_res=new FieldFunctionTuple(
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::set_m_camera_res,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::get_m_camera_res,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::getClassName,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::getFieldName_m_camera_res,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::getFieldTypeName_m_camera_res,
            &TypeFieldReflectionOparator::TypeCameraComponentOperator::isArray_m_camera_res);
        REGISTER_FIELD_TO_MAP("CameraComponent", f_field_function_tuple_m_camera_res);
```

注册完成后是启动全局变量g_runtime_global_context的系统初始化。
```c++
g_runtime_global_context.startSystems(config_file_path);
```
RuntimeGlobalContext是一个定义在GlobalContext模块的全局变量,主要成员变量是各个子模块管理器的智能指针。
```c++
        std::shared_ptr<LogSystem>       m_logger_system;
        std::shared_ptr<InputSystem>     m_input_system;
        std::shared_ptr<FileSystem>      m_file_system;
        //...
        std::shared_ptr<RenderSystem>    m_render_system;
        std::shared_ptr<ParticleManager> m_particle_manager;
```

## engine->initialize

似乎还没有实现这个函数，在engine.cpp中定义如下：
```c++
void PiccoloEngine::initialize() {}
```
## editor->initialize
这里会调用editor的构造函数
```c++
Piccolo::PiccoloEditor* editor = new Piccolo::PiccoloEditor();
```
构造函数里面通过registerEdtorTickComponent函数注册了编辑器需要tick的组件(暂时只有TransformComponent和MeshComponent)
```c++
void registerEdtorTickComponent(std::string component_type_name)
{
    g_editor_tick_component_types.insert(component_type_name);
}
```
而`g_editor_tick_component_types`是一个定义在engine.cpp的全局unordered_set.(元素的值同时也是唯一标识它的键。键是不可变的，因此，unordered_set中的元素在容器中不能被修改，但是它们可以被插入和删除。)
```c++
std::unordered_set<std::string> g_editor_tick_component_types {};
```
接着是editor初始化，这个函数接受一个engine指针变量参数。
```c++
editor->initialize(engine);
```
edior内部保存了一个engine的指针，赋值为传进来的engine指针。
初始化窗口（封装glfw），初始化Imgui，分别调用以下成员的初始化函数。
```c++
g_editor_global_context.initialize(init_info);
//...
m_editor_ui->initialize(ui_init_info);
```

## editor->run
分别调用editor context的tick函数和engine的tick函数。
```c++
while (true)
    {
        delta_time = m_engine_runtime->calculateDeltaTime();
        g_editor_global_context.m_scene_manager->tick(delta_time);
        g_editor_global_context.m_input_manager->tick(delta_time);
        if (!m_engine_runtime->tickOneFrame(delta_time))   return;
    }
```

## editor->clear
这一步只会用delete来释放掉g_editor_global_context中的以下两个成员：
```c++
class EditorSceneManager* m_scene_manager {nullptr};
class EditorInputManager* m_input_manager {nullptr};
```
## engine->clear
这个函数似乎还没有实现
## engine->shutdownEngine
·释放掉所有g_runtime_global_context的子系统智能指针所指向的资源，并且设为空指针。
·释放掉所有reflection相关的tuple。