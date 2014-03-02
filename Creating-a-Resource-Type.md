# Setup
Creating a resource is straight forward and takes 3 steps.

1. Include `ResourceSystem.h` and inherit from `ResourceBase`
2. Implement the virtual method `bool Initialize(const std::vector<Property> &properties);`
3. Add the following methods in the header file:
 * `template <> inline const char* GetTypeName<MyType>() { return "MyType"; } // Human readable name`
 * `template <> inline const unsigned int GetTypeID<MyTpe>() { return 1000; } // ID number used internally`
4. Call `resource::ResourceSystem::GetInstace()->Register<MyType>();`

# Loading, Creating, and Retrieving.
After you have setup your resource type you can load or create resources of that type.

If you can provide the type at compile time, you can call `resource::ResourceSystem::GetInstace()->Create<MyType>(std::string name, std::vector<Property> properties);` where name is the name of the resource, and properties is the creation properties.

If you don't know the type at compile time and instead load it at runtime, you can call

1. `resource::ResourceSystem::GetInstace()->GetTypeIDFromName(std::string name);` Where name is the name of the type defined in GetTypeName.
2. `resource::ResourceSystem::GetInstace()->Load(ID, std::string name, std::vector<Property> properties);`  Where ID is the ID retrieved in the above step, name is the name of the resource, and properties is the creation properties.

If the resource is already loaded you can call `resource::ResourceSystem::GetInstace()->Get<MyType>(std::string name);` Where name is the name of the resource.