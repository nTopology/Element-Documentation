#How to add a new `NTTool`

##1. In NTEnums.h, add a new entry to the NTToolType enum:

`NEW_TOOL = 200`


##2. Create the .h & .cpp with the tool's name & add the following classes:

###2-A. add the main tool class:

```c++
class NewTool :
  public NTMaker
{
public:
  NewTool();

  //override the following mandatory functions
  juce::Component* getGui();
  NTMakerData getData() const;
  bool run(const NTParamSet& params);
  std::shared_ptr<NTParamSet> getParams();
  String getDefaultName();

  //override the following optional functions
  void paint(Graphics& g, int height, int width);
  void renderOpenGL(int width, int height);
  void applyPressed(){};
  void okPressed(){};
  void cancelPressed() {};
  void updateOutput(SceneObject& output,const NTParamSet& params){}
  void updateOutputs(const std::vector<SceneObject*>& outputs,const NTParamSet& params){}
  void inputsChanged(int index){};
  void glTouchSelEvent(GLTouchObject* touchObj) {}

private:
  int mType;
};
```

###2-B. add the input params:

```c++
struct NewToolParams: public NTParamSet{

  //Add the inputs here:
  NTObjectID lattice;

  NTToolType getToolType() const override {return NTToolType::NEW_TOOL;}
  std::vector<NTObjectID> getObjectInputs() const override { return std::vector<NTObjectID>{lattice};}

  template<class Archive>
  void serialize(Archive & ar, const unsigned int version) {
    ar & boost::serialization::base_object<NTParamSet>(*this);
    ar & lattice;
  }
};
```

###2-C. add the side view class:

```c++
class NewToolSideView :
  public Component,
  public ButtonListener
{
public:

  NewToolSideView(NewTool* tool);
  void paint(Graphics& g)override;
  void buttonClicked(Button* b)override;
  void resized()override;

private:
  NewTool* mTool;
  ScopedPointer<NTButton> mNewToolButton;
  friend class NewTool;
};
```
###2-D Implement the getData() function for your tool:

```c++
NTToolData StochasticLatticeGenerator::getData() const
{
  return NTToolData{
    "Stochastic Lattice Generator",
    130,
    {{ NTInputType::MESH_CADBODY, NTInputSubType::PASSIVE },
      { NTInputType::MODIFIER_POINT, NTInputSubType::PASSIVE }},
    { NTOutputType::LATTICE, NTOutputSubType::SINGLE },
    {"Select volume","Select modifier"},
    {"Volume*", "Modifier"}
  };
}
```
Passive input means that drawing of the inputs is handeled for you. Passive is what most tools should use.

##3. Add the icon for the new tool to TopBarRibbon.h :

in the .h:
`ScopedPointer<DrawableButton> mNewToolButton;`

in the .cpp constructor:

```c++
  addAndMakeVisible(mNewToolButton = new DrawableButton("New Tool", DrawableButton::ButtonStyle::ImageStretched));
  mNewToolButton->addListener(this);
  mNewToolButton->setTooltip("Select a new tool.");
  setDrawableFromBinaries(mNewToolButton.get(),
                          BinaryData::ribbon_NEWTOOL_svg,
                          BinaryData::ribbon_NEWTOOL_hover_svg,
                          BinaryData::ribbon_NEWTOOL_down_svg);
```

if the tool is for Pro only, add a new license for the feature in:
`TopBarRibbon::updateProButtonText()`

##4. Add to SceneObject.cpp the openEdit functions:

In `SceneObject::OpenEdit()` add the following in the big If...Else:

```c++
 else if(tooltype == NTToolType::NEW_TOOL){
    auto paramSet = dynamic_cast<NewToolParams*>(mParamSet.get());
    auto newTool = new NewTool();
    outliner->setOverWriteObject(this,NTDrawMode::NORMAL,false);
    mc->activateTool(newTool);
    newTool->open(paramSet,*this);
  }
  ```
