#How to add a new `SceneObject`

##1. In SceneObject.h, add a new entry to the ObjectType enum:

`NEW_SCENE_OBJECT = 200`

& in SceneObject.cpp, add a new case to `getName(ObjectType type)` :

```
case NEW_SCENE_OBJECT:
  return "New Scene Object";
```

##2. Add the Scene Object class that subclasses from `SceneObject` :

```
class NewSceneObject :
  public SceneObject,
  public SelectableObject //Add this is the object is selectable
{
  public:
    NewSceneObject(); //Be sure to set the type to the enum val from step 1
    ~NewSceneObject();

    //Be sure to override the following functions:

  void draw(const cinder::Camera& cam)override;

  void drawNakedWireFrame()override;
  void drawNaked()override;
  void drawInActive(const cinder::Camera& cam)override;

  void updateColors(const std::vector<NTModifierPoint*>& modPoints) override;

  void killGLObjects()override;

  public: //for Selectable Object
  std::vector<std::shared_ptr<SelectionNode>> getNodesSelection()const override;
  std::vector<std::shared_ptr<SelectionFace>> getFacesSelection()const override;
  std::vector<std::shared_ptr<SelectionFace>> getTriFacesSelection()const override;

  std::vector<std::shared_ptr<SelectionEdge>> getEdgesSelection()const override;
  std::vector<glm::vec3> getNodes()const override;

  public: // save functions
  SceneObject_SaveData* write() override;
  void read1(SceneObject_SaveData* saveData, Opener *opener)override;
};
```

##3. Add the object info class:

```
class NewSceneObjectAttInfo :
  public Component
{
public:
  NewSceneObjectAttInfo(NewSceneObject* sceneObject);
  void setDelegate(TreeDelegate_SceneObject* sceneObjectDelegate) {
    mDelegate = sceneObjectDelegate;
  }

  void paint(Graphics& g)override;
  void resized()override;
 };
```

##4. Add the save data for the class:

```
 struct NewSceneObject_SaveData :
  public SceneObject_SaveData
{
  double valToSave

  size_t getSize()const override {
    return 100000;
  }

  template<class Archive>
  void serialize(Archive & ar, const unsigned int version) {
    ar & boost::serialization::base_object< SceneObject_SaveData >(*this);
    ar & valToSave;
  }
};

//Be sure to add the versioning for the saveData
BOOST_CLASS_VERSION(NewSceneObject_SaveData, 0)
```

in the .cpp add the following:

`BOOST_CLASS_EXPORT(NewSceneObject_SaveData)`

##5. Handle the saving & loading of the object in "ElemIO.cpp":

    In `Opener::run()` find the loop `for (auto &sd : saveData.mSaveDatas)`
    & add an additional else if:

    ```
        else if (sd->type == SceneObject::NEW_SCENE_OBJECT) {
      so = std::make_shared<NewSceneObject>();
    }
    ```

##6. Add a new input type for the new object in NTEnums.h in the enum:
`NTInputType`

`NEW_SCENE_OBJECT_INPUT = 200`

##7. Handle the selection of the new input in Outliner.cpp:

In the function `Outliner::mouseDown` find `if(outliner->getInputBox() && flags == ModifierKeys::leftButtonModifier){` & inside of that if statement, add a new `else if` :

```
else if(IType == NTInputType::NEW_SCENE_OBJECT_INPUT){
        if(so->getType() == SceneObject::NEW_SCENE_OBJECT){
          outliner->getInputBox()->addSceneObject(so);
        }
      }
```


