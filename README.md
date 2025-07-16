# Welcome to the Advanced Inventory System

## This project is a practice program attempting to model an Inventory System of a video game using **Unreal Engine 5.**

The Inventory Systems main goal is to serve as both a _practice and training exercise_ for myself and as a guide for other that wish to implement something similar into their games/projects. This Inventory includes the following features:
* Stackable and Usable Items
* Chests and Contatiners with their own inventory.
* Transfering of items between slots and container inventroies
* Dropping items individually or in stack, the stack generates a "Loot Bag" with their own inventory.
* A hot bar in which you can drag items to Bind them to the hotbar slot
* An attempt of a save/loading system in which your inventory is saved at the end of a Game and loads the inventory at the beginning of the game
* An attempt of a save/load system where it saves the position of dropped items.

This document is meant to be a documentation of all the features as well as an explanation using Pseudo code for all the added elements of the project.

# Content

<details>

  <summary>Syntaxis, Diagrams and Media</summary>

  # Syntaxis, Diagram and Media

  ## How is this document organized and how should you read it?
  
  This section is to give an understanding of how the document will be formated and organized as well as explaining the diagrams that will be placed around this document.

  ### Syntax

  All of the added or edited functions, structures or events will be added to this document. Any default classes, structures, etc, that are in the base Unreal Engine default projects, will only be added if they are specifically mentioned in the context of the coding.

  The coding will be done in a pseudocode that will ressemble as much as possible a object oriented language

  Example:

  ```
    class newClass {

      public  bool attribute;
      public  int attribute2;
      private static  struct attribute3;

      public function1( int parameter, bool parameter){
        return returnType;
      }

      private functionEvent(){
        attribute += 1;
      }
    }
  ```
  
  ### Diagrams 
  
  There will be general UML Diagrams summarizing and showing the logic of every component, as Unreal has very specific color coding for their nodes (i.e, events, functions, and the specific color of the data structures) these diagrams will use the colors used in Unreal for the classes and data structures.
  
  Example:

  ![imagen](https://github.com/user-attachments/assets/c4eb07d7-86af-498c-9c1a-d473067e88b4) [^1]

  [^1]: Example of a diagram that will be used in this document

### Media

Media in this document will have foot notes describing what the media is showing. Media can include images, videos of the project to assist in the understanding of this project.

Example:

  ![dotboxspawnemitter](https://github.com/user-attachments/assets/a0b89f3c-2c57-42c8-b502-7a0211c958d8)[^2]

  [^2]: Example of an image showing an Unreal Project taken from the [Unreal Documentation Webpage](https://dev.epicgames.com/documentation/en-us/unreal-engine/nodes-in-unreal-engine). 
  
</details>

<details>
  
  <summary>Inventory System</summary>

  # Inventory_System

  ## The main function of the system

  This function is the core of this project, it is the system in which items are added, removed, transfered, used, etc.

 ![imagen](https://github.com/user-attachments/assets/3aceb936-677c-483e-8894-e7a6ea9ac396) [^3]
  
  [^3]: Inventory System Class Diagram

  ### Project Settings

  For this structure you will need to add an input into your Project settings to Interact with items, in this case we will use the "i" key to give the ability to interact with pickable items
  ![imagen](https://github.com/user-attachments/assets/b75ad283-0a00-4999-af1e-020f104a5118) [^4]

  [^4]: Image showing the project setting where in Inputs, we add the Interact Input that is set to the "i" key.

  ```
  #import F_Slot_Struct
  #import W_Display_Message
  #import SaveData-Level
  #import Loot_Bag
  #import Item_Data
  #import F_Container_Struct

  public class Inventory System {
    private int inventorySize = 16 
    private F_Slot_Struct[] content;
    private float interactionTrace = 300.0;
    private Actor lookAtActor;
    private W_Display_Message displayMessage;
    ...

  }
  ```

  <summary>Event Graph</summary>

  ### Event Graph

  This section show every single event in the Inventory System blueprint class.

  <ins>Event Begin Play</ins>

  The Begin Play event starts at the beginning of the project. It is able to load any previous saved inventories, generate the inventory size, add a display message to the viewport when an item is interactable, and auto saves the inventory. 

  ![imagen](https://github.com/user-attachments/assets/883fade5-97d6-4d10-bf7f-b4d5e6b991de)[^5]

  [^5]: Begin Play Event in Unreal Engine 5

  ```
  public class Inventory System {

    private int inventorySize = 16 
    private F_Slot_Struct[] content;
    private float interactionTrace = 300.0;
    private Actor lookAtActor;
    private W_Display_Message displayMessage;

    private eventDispatch On_Inventory_Update; // Event Dispatcher, allows multiple events to happens at once

    // starts at the beginning of the project
    public event Event_Begin_Play(){
      LoadInventory(self);
      content.resize(inventorySize); //sets the contents array size to 16
      displayMessage = createWidget(classtype.W_Display_Message);
      addToViewport(displayMessage);
      bindEventTo(On_Inventory_Update, AutoSave_Inventory);
    }
  }
  ```

  <ins>Event Tick</ins>

  This Event updates every frame and checks the interaction trace to see if there is an interactable item in front of the player.

  ![imagen](https://github.com/user-attachments/assets/82d3271a-62f8-43c7-8dd1-2f07f0346b82) [^6]

  [^6]: Tick Event in Unreal Engine 5

  
  ```
   public event Tick(){
    interactionTrace(self); //Check if an interactive item is currently in the players interactive distance
   }
  ```

  <ins>Event InputAction Interact</ins>

  This Event triggers every time the player interacts with an item with the Inpu "I".

  ![imagen](https://github.com/user-attachments/assets/06a68e27-e3be-416e-9f26-68112ac35407) [^7]

  [^7]: InputAction Interact Event in Unreal Engine 5

  ```
   public event InputAction_Interact(){ 
    switch(input Interact){ //checks if the input is pressed or released
        case "pressed":
          if(lookAtActor.isValid){
            Server_Interact(self, lookAtActor)
          }
      }
   }
  ```

 <ins>Event Server_Interact</ins>

 This is a very specific event, it checks if a player is interacting from server or client side with an object, if the object is on server side it will update as so, if it's from client, it will declare the object as client side and set the owner as client.

 ![imagen](https://github.com/user-attachments/assets/74e047bc-201f-4633-9280-847860b07435) [^8]

  [^8]: Server_Interact Details in Unreal Engine 5

 ![imagen](https://github.com/user-attachments/assets/daba19c7-25b6-48fe-b60e-af8fd5c79e76) [^9]

 [^9]: Server_Interact Event in Unreal Engine 5

 ```
  public event Server_Interact(actor){
    BP_ThirdPersonCharacter owner = (BP_ThirdPersonCharacter) self.getOwner; //We get the owner of the player and cast the value as BP_ThirdPersonCharacter type Objecct
    if(target.getComponentByClass(Item_Data_Component).isValid){
      Interact_With(target, owner); //This will go to the Interact with Event algonside with the target object and the owner  
    } else {
      setOwner(target, owner.getController); // This sets the client player controller as owner of the object
      OnLocalInteract(self, target, owner);
    }
  }
 ```

<ins>Event MC_Update</ins>

It simply updates any inventory changes

![imagen](https://github.com/user-attachments/assets/bed2a15e-f858-42c9-94a7-a7128949bf6f) [^10]

[^10]: MC_Update Event in Unreal Engine 5

```
  public event MC_Update(){
    On_Inventory_Update.callOn(self);
  }
```

<ins>Event "I"</ins>

A simple debugging event that triggers everytime "I" is pressed.

![imagen](https://github.com/user-attachments/assets/fb6627fe-2f49-49e9-b597-0f569f7ccc5a) [^11]

[^11]: "I" Event in Unreal Engine 5

```
  public event I(){
    DEBUGPrintContent();
  }
```

<ins>Event Server_Transfer_Slots</ins>

This Event updates when there is a transfer in the inventory slots of one or multiple inventories.

![imagen](https://github.com/user-attachments/assets/ae0f49cd-2681-4823-8d3a-d82089881db4) [^12]

[^12]: Server_Transfer_Slots Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/aee8b24f-758e-40bb-9568-00fd2f524a20) [^13]

[^13]: Server_Transfer_Slots Event in Unreal Engine 5

```
  public event Server_Transfer_Slots(int sourceIndex, Inventory_System sourceInventory,int destinationIndes){
    TransferSlots(self, sourceIndex, spurceInvenotry, destinationIndex);
  }
```

<ins>Event OnLocalInteract</ins>

This Event triggers when the player locally interacts with the object

![imagen](https://github.com/user-attachments/assets/c450e0e0-c2c7-4ce5-87b5-a31151a0e44d) [^14]

[^14]: OnLocalInteract Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/2600df71-df9f-41fe-ae80-1e118977a99a) [^15]

[^15]: Server_Transfer_Slots Event in Unreal Engine 5

```
  public event OnLocalInteract(actor targetActor, actor interactor){
    interactWith(targetActor, (BP_Third_Person_Character) interactor);
  }
```

<ins>Event Server_Remove</ins>

This Event triggers when the player removes an item from their inventory

![imagen](https://github.com/user-attachments/assets/ff1d9ff9-c840-4126-8ceb-34284712acfc) [^16]

[^16]: Server_Remove Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/62c430bf-8abf-41ba-8c24-6d2468d6a19e) [^17]

[^17]: Server_Transfer_Slots Event in Unreal Engine 5

```
  public event Server_Remove(int index, bool removeWholeStak, bool isConsumed){
    RemoveFromInventory(index, removeWholeStask, isConsumed);
  }
```

<ins>Event Server_Drop_Item</ins>

This Event triggers when the player drops an item, for both server and client side, this also saves where these dropped items are placed so they are loaded in the next load of the game

![imagen](https://github.com/user-attachments/assets/a42db1fd-c758-43cd-b7d4-b5a4ed8578b4) [^17]

[^17]: Server_Drop_Items Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/21da8828-176c-44e2-be95-6b1f3b8bfa1b) [^18]

[^18]: Server_Drop_Item Event in Unreal Engine 5

```
  public event Server_Drop_Item(name itemID, int quantity){
    if(quantity > 1){
      AddToInventory(spawnActor(/*Class=*/ classType.Loot_Bag,/*SpawnTransformLocation=*/ getDropLocation()).Inventory_System, itemID, quantity); // This will generate a loot bag with the amount of items that were dropped
    } else {
      actor droppedActor = spawnActor(/*Class=*/ getItemData.itemClass, /*SpawnTransformLocation=*/ getDropLocation()); //it will create a new actor with the item characteristics and position where it was last placed
      delay(.2); // This delay is added to allow the game to save apporpirately with entering a nullPointerException
      Save_Data_Level saveData = ((BPThirdPersonGameMode) getGameMode()).Save_Data_Level; //We have the save data of Game
      add(saveData.actorAdded, (soft reference) getItemData.itemClass, getActorTransform(droppedActor)); //we add the dropped items to the save data distionary
      saveGameToSlot(saveData, ((BPThirdPersonGameMode) getGameMode()).levelDataSlot); //we save the game in a save file
      DEBUGPrintContent();
    }
  }
```

<ins>Event Server_Consume_Item</ins>

This Event triggers when the player consumes an item and it gives them an effect on server and client side

![imagen](https://github.com/user-attachments/assets/a42db1fd-c758-43cd-b7d4-b5a4ed8578b4) [^19]

[^19]: Server_Consume_Item Details in Unreal Engine 5

![imagen](https://github.com/user-attachments/assets/496cf6bf-bbc8-49c3-9ed7-bab2e3eff8df) [^20]

[^20]: ServerConsume_Item Event in Unreal Engine 5

```
  public event Server_Drop_Item(name itemID){
    spawnActor("Class=" getItemData(itemID).itemEffect, "SpawnTransfromLocation"= getDropLocation());
  }
```

<ins>Event AutoSave_Inventory</ins>

This Event triggers everytime the player loads the game, it saves to teh current save file

![imagen](https://github.com/user-attachments/assets/4b9750c2-9abe-4894-a112-52e3c8649232) [^21]

[^21]: AutoSave_Inventory Event in Unreal Engine 5

```
  public event AutoSave_Inventory(){
    SaveInventory();
  }
```
  
<summary>AddToInventory</summary>

This is a function that allows the player to add items to their inventory.

![imagen](https://github.com/user-attachments/assets/9eb9240e-4f3f-4d46-862a-92913472c7c8) [^22]

[^22]: addtoInventory function in Unreal Engine 5

```
public AddToInventory(name itemID, int quantity){
  boolean localHasFailed;
  int localQuantityRemaining = quantity;
  while(localQuantityReaming > 0 AND !localHasFailed){
    if(FindSlot(itemID).foundSlot){
      AddToStack(findSlot(itemID).index, 1);
      localQuantityRemaining--;
    } else {
      if(AnyEmptySlotsAvailable().hasEmptySlot){
        if(CreateNewStack(itemID, 1).success){
          localQuantityRemaining--;
        } else {
          localHasFailed = true;
        }
      }else {
        localHasFailed = true;
      }
    }
  }
  callOnInventoryUpdate();
  return boolean success = !localHasFailed, int quantityReaming = localQuantityReaming;
}
```

<summary>RemoveFromInventory</summary>

This is a function that allows the player to remove items froms their inventory.

![imagen](https://github.com/user-attachments/assets/56e0d81b-a76b-4a77-b256-c75e86873c83) [^23]

[^23]: removeFromInventory function in Unreal Engine 5

```
public RemoveFromInventory(int index, boolean removeFromWholeStack, isConsumed){
  int localQuantity = content.itemName;
  name localItem = content. quantity;
  sequence {
    then 0:
      if(removeFromWholeStack OR localQuantity == 1){
        setArrayElem(content, index);
        if(!isConsumed){
          serverDropItem(localItem, localQuantity);
        }
      } else {
        setMembers(content[index],content[index].quantity-1);
        if(!isConsumed){
          serverDropItem(localItem, 1);
        }
      }
    then 1:
      MCUpdate();
  }
}
```

<summary>InteractionTrace</summary>

This is a function that detects when a player is the proximity of an interactable item, this is done so when the player is lookingat an item, the code will detect the item.

![imagen](https://github.com/user-attachments/assets/d18a760a-7cc8-4286-99a2-feb4972668f6) [^24]

[^24]: interactionTrace function in Unreal Engine 5

```
public InteractionTrace(){
  SphereTrace  sphereTrace = sphereTraceByChannel(/*start=*/(getActorLocation(getPlayerCharacter))-Vector3(0.0, 0.0, 65.0),
                                    /*end=*/ ((getActorLocation(getPlayerCharacter()))-Vector3(0.0, 0.0, 65.0) + (getActorForwardVector(getPlayerCharacter()) * interactionTrace)),
                                    /*radius=*/ 25.0,
                                    /*traceChannel=*/ "Interactive",
                                    /*traceComplex=*/ false,
                                    /*actorsToIgnore=*/makeArray(getOwner()),
                                    /*drawDebugType=*/ null,
                                    /*ignoreSelf=*/ true);
  if(sphereTrace.returnValue){
    if(sphereTrace.outHit.hitActor != lookAtActor){
      lookAtActor = sphereTrace.outHit.hitActor;
      showMessage(displayMessage, lookAt(lookAtActor));
    }
  } else {
    lookAtActor = null;
    showMessage(displayMessage, "");
  }
}
```

<summary>FindSlot</summary>

This is a function that searches for any slots that have items in the inventory.

![imagen](https://github.com/user-attachments/assets/038700db-7085-4114-b31e-fd9766aa9f7c) [^26]

[^26]: findSlot function in Unreal Engine 5

```
public FindSlot(name itemID){
  forEach x in content{
    if(content[x].itemName == itemID){
      if(content[x].quantity == itemID.GetMaxStackSize()){
        return int index = content[x].arrayIndex, boolean foundSlot = true;
      }
    }
  }
  return int index = -1, boolean foundSlot = false;
}
```

<summary>GetMaxStackSize</summary>

This is a function that checks if a slot has reached the maximum amount of the item in the slot;

![imagen](https://github.com/user-attachments/assets/032a6ee3-c843-4a89-a98e-04ff102da015) [^27]

[^27]: GetMaxStackSize function in Unreal Engine 5

```
public GetMaxStackSize(name itemID){
  switch(getDataTableRow("Data_Table=" Item_Data, itemID)){
    case "Found":
      return int stackSize = getDataTableRow("Data_Table=" Item_Data, itemID).stackSize;
    case "NotFound":
      return int stackSize = -1;
  }
}
```

<summary>AddToStack</summary>

This is a function that adds an item to a slot.

![imagen](https://github.com/user-attachments/assets/8d03f842-b4d7-4993-9040-491153088680) [^28]

[^28]: AddtoStack function in Unreal Engine 5

```
public AddToStack(int index, int quantity){
  setArrayElem(content, index, makeF_Slot_Struct(content[index].itemName, content[index].quantity + quantity));
}
```

<summary>AnyEmptySlotsAvailable</summary>

This is a function that checks if the player has any empty slots in their inventory.

![imagen](https://github.com/user-attachments/assets/71d05f4d-30f4-4ca2-a83f-8307a92be273) [^29]

[^29]: AnyEmptySlotsAvailable function in Unreal Engine 5

```
public AnyEmptySlotsAvailable(){
  forEach x in content{
    if (content[x].quantity == 0){
      return boolean hasEmpltySlot = true, int emptyIndex = x;
    }
  }
  return boolean hasEmpltySlot = false, int emptyIndex = -1;
}
```

<summary>CreateNewStack</summary>

This is a function that creates a new stack

![imagen](https://github.com/user-attachments/assets/4fea3ece-ddf2-414d-8203-28a046b2ddab) [^30]

[^30]: CreateNewStack function in Unreal Engine 5

```
public CreateNewStack(name itemID, int quantity){
  if(AnySlotsAvailable().hasEmptySlot){
    setArrayElem(content, AnyEmptySlotsAvailable().emptyIndex, makeF_Slot_Struvt(itemID, quantity));
    return boolean success = true;
  } else {
    return boolean success = false;
  }
}
```


<summary>DEBUGPrintContent</summary>

This functions' sole purpose is meant for troubleshooting, and has no defined code, overall it can be whatever purpose needed to be. althoug the output will always be a print function

```
public DEBUGPrintContent(){
  ...
  ...
  printString(...);
}
```


<summary>TransferSlots</summary>

This function allows the player to transfer content between slots, add items to slots that do not go over the maximum stack size, adn transfer items between inventories.

![imagen](https://github.com/user-attachments/assets/f6a56939-56e3-467a-b5dd-050a913ba990) [^31]

[^31]: TransferSlot function in Unreal Engine 5


```
public TransferSlots(int sourceIndex, Inventory_System sourceInventory, int destinationIndex){
  F_Slot_Struct localSlotContent = sourceIntventory.content[sourceindex];
  if((destinationIndex < 0) == false){
    //Check if there is a same item in the destination slot to add it without going the max stack size
    if(localSlotContent.itemName == content[destinationIndex].itemName){
      int addedQuantity = localSlotContent.quantity + content[destinationIndex].quantity;
      int maxStackSize = GetMaxStackSize(localSlotContent.itemName);
      //Source Index
      setArrayElem(sourceInventory.content,
                  sourceIndex,
                  select(/*False=*/ None, /*True=*/ localSlotContent.itemName, /*Index=*/ clamp(addedQuantity - maxStackSize, 0, maxStakSize) > 0),
                  clamp(addedQuantity - maxStackSize, 0, maxStakSize));
      //Destination index
      setArrayElem(content,
                  destinationIndex,
                  localSlotContent.itemName,
                  clamp(addedQuantity, 0, mazStackSize));
      MC_Update(self);
      MC_Update(sourceIndex);
    } else {
      //source Index
      setArrayElem(sourceInventory.content, sourceIndex, content[destination]);
      //destination Index
      setArrayElem(content, destinationIndex, loclaSlotContent);
      MC_Update(self);
      MC_Update(sourceInventory);
    }
  }
}
```

<summary>GetItemData</summary>

This function allows to get the item data of a specified item.

![imagen](https://github.com/user-attachments/assets/8678386c-a5dc-4746-a613-9527b3152b31) [^32]

[^32]: GetItemData function in Unreal Engine 5


```
public GetItemData(name itemID){
  return F_Item_Struct itemData = getDataTableRow(Item_Data, nameID);
}
```

<summary>GetDropLocation</summary>

This function allows to get the position in which a player drops an item.

![imagen](https://github.com/user-attachments/assets/a7e92f38-f9f3-4ebb-9469-b9adf8823664) [^33]

[^33]: GetDropLocation function in Unreal Engine 5


```
public GetDropLocation(){
  return Vector3 location = lineTraceByChannel( /*Start=*/ getActorLocation(getOwner()) + (randomUnitVectorinConeinDegrees(getActorForwardVector(getOwner), 30.0) * 150.0),
                                              /*End=*/ getActorLocation(getOwner()) + (randomUnitVectorinConeinDegrees(getActorForwardVector(getOwner), 30.0) * 150.0) - Vector3(0.0, 0.0, 500.0),
                                              /*Trace Channel=*/ "Visibility;
                                              /*Ignore Self=*/ true).location;
}
```

<summary>ConsumeItem</summary>

This function allows the player to consume an item if it is consumable.

![imagen](https://github.com/user-attachments/assets/d8906a64-b90e-4198-9c74-bfcc94339ca5) [^34]

[^34]: ConsumeItem function in Unreal Engine 5


```
public ConsumeItem(int index){
  name localItemID = content[index].itemName;
  int localQuantity = content[index].quantity;
  sequence {
    then "0":
      Server_Remove(self, index, false, true);
      Server_Consume_Item(self, localItemID);
    then "1":
      MC_Update():
  }
}
```

<summary>QueryInventory</summary>

This function allows for a search of a specific item in the inventory.

![imagen](https://github.com/user-attachments/assets/fc940594-296f-4205-b718-446e61acb3a2) [^35]

[^35]: QueryInventory function in Unreal Engine 5


```
public QueryInventory(name itemID, int quantity){
  int runningTotal = 0;
  forEach x in content{
    if(content[x].itemName == itemID){
      runningTotal += content[x].quantity;
    }
  }
  return int oldQuantity = runningTotal, boolean success = runningTotal >= quantity
}
```

<summary>GetQuantity</summary>

This function gets the total items in an inventory.

![imagen](https://github.com/user-attachments/assets/e02e806b-38b0-4bbb-a1d8-e350e5493fdc) [^36]

[^36]: GetQuantity function in Unreal Engine 5


```
public GetQuantity(){
  int localSum = 0;
  forEach x in content{
    localSum += content[x].quantity
  }
  return int total = localSum;
}
```

<summary>SaveInventory</summary>

This function allows the game to save all states of the current inventories in existance.

![imagen](https://github.com/user-attachments/assets/b0f48d69-f2ea-4b3b-a10c-461cb69f05ee) [^37]

[^37]: SaveInventory function in Unreal Engine 5


```
public SaveInventory(){
  if(getOwner() == getPlayerCharacter(0)){
    F_Slot_Struct inventoryContent[] = set(content, ((My_Game_Instance) getGameInstance()).SaveData-PlayerData);
    SaveGametoSlot(((My_Game_Instance) getGameInstance()).SaveData-PlayerData, "MyData", 0);
  } else {
    add(/* target array*/ ((My_Game_Instance) getGameInstance()).SaveData-PlayerData,/*invenotry system*/ self,/*item to be added*/ makeF_Container_Struct(content));
  }
}
```

<summary>LoadInventory</summary>

This function allows the game to load all saved inventories.

![imagen](https://github.com/user-attachments/assets/d71f4a07-27af-4dc6-8066-276b89ed6d69) [^38]

[^38]: LoadInventory function in Unreal Engine 5


```
public LoadInventory(){
  if(getOwner() == getPlayerCharacter(0)){
   content = ((My_Game_Instance) getGameInstance()).SaveData-PlayerData.inventoryContent
  } else {
    content = find(/* target array*/ ((My_Game_Instance) getGameInstance()).SaveData-PlayerData,/*invenotry system*/ self).contents;
  }
}
```

</details>

<details>

<summary>F_Item_Struct</summary>

# F_Item_Struct

## What's in an item

This is a structure that defines what data an item has, this allows us to alter these data and move items around without much issue;.

### Structure Elements

![imagen](https://github.com/user-attachments/assets/75ff2d91-a444-412c-ad1f-9366f9cacea0) [^39]

[^39]: F_Item_Struct class diagram

![imagen](https://github.com/user-attachments/assets/fc7f6703-36bb-4522-bfe8-31c6d8bf71e2) [^40]

[^40]: F_Item_Struct attributes in Unreal Engine 5

```
struct F_Itemt_Struct {
  text name,
  text description,
  Texture2D thumbnail,
  actorClass itemClass,
  int stackSize,
  BP_Item_Effect itemEffect,
  category E_Item_Category
}
```
</details>

<details>

<summary>E_Item_Category</summary>

# E_Item_Category

## What kind of item is this

This is an enumator that classifies the items to different categories.

### Enumator 

![imagen](https://github.com/user-attachments/assets/f170cdfd-7f6b-42a6-81c2-6bfa2f110288) [^41]

[^41]: E_Item_Category class diagram

![imagen](https://github.com/user-attachments/assets/8f6881f2-e6a6-447e-9eb7-2b7c87394908) [^42]

[^42]: E_Item_Category Values in Unreal Engine 5

```
enum E_Item_Category {
  Food,
  Material,
  Equipment,
  Potion,
  Junk
}
```
</details>

<details>

<summary>F_Slot_Struct</summary>

# F_Item_Struct

## What's can we change in the item slot?

This is a structure that defines what data is being shown for an item slot to be altered and changed in the code

### Structure Elements

![imagen](https://github.com/user-attachments/assets/18bf0fbf-6a22-44da-afbf-8fc23b4907b3) [^43]

[^43]: F_Slot_Struct class diagram

![imagen](https://github.com/user-attachments/assets/4cde0281-3092-4e31-929e-18314f552df9) [^44]

[^44]: F_Slot_Struct attributes in Unreal Engine 5

```
struct F_Slot_Struct {
  name itemID,
  int quantity
}
```
</details>

<details>

<summary>I_Interact_Interface</summary>

# I_Interact_Interface

## How can we interact

This is an interface with the sole purpose of giving certain functions that allows players to interact with certain objects.

### interface 

![imagen](https://github.com/user-attachments/assets/4d552861-cad2-4aec-b845-8016ccd89038) [^45]

[^45]: I_Interact_Interface class diagram

![imagen](https://github.com/user-attachments/assets/23199bf2-8c3a-43d8-80ac-6d57c36672c9) [^46]

[^46]: I_Interact_Interface in Unreal Engine 5

```
public class interface E_Item_Category {

  public lookAt();

  public interactWith();
}
```
</details>

<details>

<summary>DD_Inventory_Slot</summary>

# DD_Inventory_Slot

## Drag and Drop items

This is an Drag and Drop blueprint structure that allows the player to drag and drop items from their inventories

### Class structure

![imagen](https://github.com/user-attachments/assets/2527deb8-f745-4c3d-9508-e507073bd3c0) [^47]

[^47]: DD_Inventory_Slot class diagram

```
#import Inventory_System

public class DD_Inventory_Slot{

  Inventory_System invenotry;
  int contentIndex;

....
}
```

<summary>Event Graph</summary>

<ins>Event_DragCancelled</ins>

This event happens when the player stops the dragging of the mouse, it call the remove from inventory.

![imagen](https://github.com/user-attachments/assets/0613ea6e-10af-484c-946b-034cde26c89d) [^48]

[^48]: Event_DragCancelled event in Unreal Engine 5.

```
public event Event_DragCancelled(){
    RemoveFromInventory(inventory, contentIndex, true, false);
}
```

</details>

<details>

<summary>Item_Data</summary>

# Item_Data

## Every single item

This is a Data Table with the F_Data_Structure Data base, this allows us to add different types of items into the project.

### Current Data.

![imagen](https://github.com/user-attachments/assets/ca572b55-6800-4e6e-959e-96091e2e8bff) [^49]

[^49]: Item_Data Data Table diagram

![imagen](https://github.com/user-attachments/assets/85d57a2e-e094-4496-a785-5a2f7db3d25a) [^50]

[^50]: Item_Data configuration in Unreal Engine 5

</details>

<details>

<summary>Item_Data_Component</summary>

# Item_Data_Component

## How we add the items to an ivnentory

This is a component blueprint that is attached to items, so anyone can add them to their inventory. It has all the data from the specific item that is being added.

### Class structure

![imagen](https://github.com/user-attachments/assets/61c242af-ee49-4672-a46c-b1b5e55c1a3c) [^51]

[^51]: Item_Data_Component class diagram

```
#import Item_Data
#import Inventory_System


public class Item_Data_Component implements I_Interact_Interface{

  Data_Table_Row itemID;
  int quantity

....
}
```

<summary>Event Graph</summary>

<ins>Event_InteractWith</ins>

This event happens when a player interacts with the item.

![imagen](https://github.com/user-attachments/assets/89d7ca00-3b1a-4a44-ad84-088109f0d39e) [^52]

[^52]: Event_InteractWith event in Unreal Engine 5.

```
public event Event_InteractWith(BPThirdPersonCharacter playerCharacter){
    if(isValid(playerCharacter.inventorySystem)){
      if(AddToInventory(playerCharacter.inventorySystem, itemID.rowName, quantity).success){
        destroyActor(getOwner());
      }
    }
}
```

</details>

<details>

<summary>Chest</summary>

# Chest

## A simple container with an inventory

An actor that functions as a chest with its own inventory System

### Class structure

![imagen](https://github.com/user-attachments/assets/7fc3f0ab-e974-4d34-ab50-eebf25779aa8) [^53]

[^53]: Chest class diagram

![imagen](https://github.com/user-attachments/assets/c5ad802e-2a17-48b4-9df0-9e6531f1a390) [^54]

[^54]: Chest Viewport in Unreal Engine 5

```
#import Inventory_System

public class Chest implements I_Interact_Interface{

  private Inventory_System Inventory_System;
  private StaticMesh Cone;
  private Scene_Component DefaultSceneRoot;

....
}
```

<summary>Event Graph</summary>

<ins>Event_InteractWith</ins>

This event happens when a player interacts with the chest, it allows to open the chest's inventory.

![imagen](https://github.com/user-attachments/assets/411133ab-7c36-48c9-8977-a528684c1c93) [^55]

[^55]: Event_InteractWith event in Unreal Engine 5.

```
public event Event_InteractWith(BPThirdPersonCharacter playerCharacter){
   try {
    ShowContainer(((MyPlayerController) GetPlayerController()).HUD, Inventory_System);
  }
}
```

</details>

<details>

<summary>Loot_Bag</summary>

# Loot_bag

## A simple container with an inventory that is made when you drop a lot of items

An actor that functions as a loot bag with an Inventory System. this actor generates when you drop more than one item to the ground. it inherits its elements from the chest actor

### Class structure

![imagen](https://github.com/user-attachments/assets/4dd1bbdf-a9f7-4b1e-8719-840674c2c3b4) [^56]

[^56]: Loot_Bag class diagram

![imagen](https://github.com/user-attachments/assets/22213439-4864-4b24-a232-3313557fb26b) [^57]

[^57]: Loot_Bag Viewport in Unreal Engine 5

```
#import Inventory_System

public class Loot_Bag extends Chest{

  private Inventory_System Inventory_System = super.Inventory_System;

....
}
```

<summary>Event Graph</summary>

<ins>Event BeginPlay</ins>

This event happens the project starts, this saves the current containers in the scene to be loaded later.

![imagen](https://github.com/user-attachments/assets/2dff7b3a-a0c5-4e81-bf06-f120ee24edec) [^58]

[^58]: Event BeginPlay event in Unreal Engine 5.

```
public event BeginPlay(){
   super.BeginPlay();
  try {
    add(((BP_ThirdPersonGameMode) getGameMode()).SaveData-Level.actorsAdded, (soft_referece) getClass(self), getActorTransform(self));
    SaveGameToSlot(((BP_ThirdPersonGameMode) getGameMode()).SaveData-Level, ((BP_ThirdPersonGameMode) getGameMode()).LevelDataSlot, 0);
  }
}
```

<ins>Event Destroyed</ins>

This event happens the loot bag is destroyed, this saves the destroyed containers in the scene to be recorded as destroyed.

![imagen](https://github.com/user-attachments/assets/b7cac0ac-9019-4b4a-8655-fe0c9047abfc) [^59]

[^59]: Event Destroyed event in Unreal Engine 5.

```
public event Destroyed(){
   super.Destroyed();
  try {
    addUnique(((BP_ThirdPersonGameMode) getGameMode()).SaveData-Level.actorsRemoved, (soft_referece) self);
  }
}
```

<ins>Event On_Inventory_Update</ins>

This event happens the loot bag's invenotry is update, if the loot bag is empty, it will be destroyed.

![imagen](https://github.com/user-attachments/assets/f304f8f4-2d16-4c1f-b7c6-4acce104d205) [^60]

[^60]: Event On_Inventory_Update event in Unreal Engine 5.

```
public event On_Inventory_Update(){
   if(Inventory_System.GetQuantity() <= 0){
    RemoveFromParent(((MyPlayerController) getPLayerController(0)).HUD);
    DestroyActor();
  }
}
```
</details>

<details>

<summary>TEST_item</summary>

# TEST_item

## A simple item blueprint for all other items

An actor that is basicaaly an item with no function or description

### Class structure

![imagen](https://github.com/user-attachments/assets/7167491a-56e0-4095-828c-2bc19e937886) [^61]

[^61]: TEST_Item class diagram

![imagen](https://github.com/user-attachments/assets/a5a7faa5-e2c6-4f1a-9760-b1ec1229ab4b) [^62]

[^62]: TEST_Item Viewport in Unreal Engine 5

```
#import Item_Data_Component

public class TEST_Item implements I_Interact_Interface{

  private Item_Data_Component Item_Data_Component;
  private StaticMesh Cube;
  private Scene_Component DefaultSceneRoot;

....
}
```

<summary>Event Graph</summary>

<ins>Event Destroyed</ins>

This event happens when a player grabs the item and is later on destroyed, this also saves to the current save file

![imagen](https://github.com/user-attachments/assets/215b7a63-e133-4ddb-ae4b-62e1dbed2a42) [^63]

[^63]: Event Destroyed event in Unreal Engine 5.

```
public event Destroyed(){
   try {
    addUnique(((BP_ThirdPersonGameMode) getGameMode()).SaveData-Level.actorsRemoved, (soft_reference) self);
  }
}
```

<summary>LookAt</summary>

The function from the I_Interact_Interface, this will show a message with the item name everytime the player is looking at an item.

![imagen](https://github.com/user-attachments/assets/1614d6f1-312d-4053-852e-1185fc9f297e) [^64]

[^64]: LookAt function in Unreal Engine 5

```
public LookAt(){
  return text Message = FormatText("Pick up " + breakF_ItemStruct(getDataTableRow(Item_Data_Component.Data_Table, Item_Data_Component.Row_Name)).name);
}
```

</details>

<details>

<summary>Apple</summary>

# Apple

## A simple apple

An apple actor that is being used to test the inventory system, it inherets all the attributes from the TEST_Item Actor

### Class structure

![imagen](https://github.com/user-attachments/assets/910e137b-9406-4646-b495-aa694ff7f3fd) [^65]

[^65]: Apple class diagram

![imagen](https://github.com/user-attachments/assets/a5a7faa5-e2c6-4f1a-9760-b1ec1229ab4b) [^66]

[^62]: Apple Viewport in Unreal Engine 5

```
#import Item_Data_Component

public class Apple implements I_Interact_Interface extends TEST_Item{

  private Item_Data_Component Item_Data_Component = super.Item_Data_Component;
  private StaticMesh Cube = super.Cube;
  private Scene_Component DefaultSceneRoot = super.DefaultSceneRoot;

  public event Destroyed{
      super.Destroyed();
  }

  public LookAt(){
    super.LookAt();
  }

}
```
</details>

<details>

<summary>BP_Item_Effect</summary>

# BP_Item_Effect

## Give Players Effects

This Blueprint is meant to give the player effects from any item that gives an effect

### BP Configuration

![imagen](https://github.com/user-attachments/assets/a63d3829-e0d4-42eb-8fd2-6b558ee66501) [^67]

[^67]: BP_Item_Effect class diagram

![imagen](https://github.com/user-attachments/assets/232127a7-995d-48e4-be92-d76c2c9e9f7c) [^68]

[^68]: BP_Item_Effect in Unreal Engine 5

```
public class BP_Item_Effect {

.....
}
```
</details>

<details>

<summary>Effect_Heal_Player</summary>

# Effect_Heal_Player

## A healing effect

This BP Effect simulates the healing of a player, it just prints a text that says it healed the player.

### BP Configuration

![imagen](https://github.com/user-attachments/assets/64d7ad2f-79de-4326-a8be-db2610816212) [^69]

[^69]: BP_Item_Effect class diagram

```
public abstract class Effect_Heal_Player extends BP_Item_Effect{
  .....
}
```

<summary>Event Graph</summary>

<ins>BeginPlay</ins>

This Event just prints "Healed Player"

![imagen](https://github.com/user-attachments/assets/d1c03196-72d2-4dd6-8aea-25e47e162197) [^70]

[^70]: BP_Item_Effect in Unreal Engine 5

```
public event BeginPlay() {
  PrintString("Healed Player");
}
```
</details>

<details>

<summary>W_action_Menu</summary>

# W_action_Menu

## Let's use our items

This is a UI that creates a menu inside the inventory. When you right click you have the option to use, drop 1, or drop all items in the slot.

### Widget Configuration

![imagen](https://github.com/user-attachments/assets/ca62b84c-7df3-4a5d-ac06-c3ea6a9a30be) [^71]

[^71]: W_action_Menu class diagram

![imagen](https://github.com/user-attachments/assets/5245f984-2063-4add-9dcb-3ffd6b3a9966) [^72]

[^72]: W_action_Menu designer in Unreal Engine 5

```
#import Inventory_System

public class W_action_Menu{

  private Vertical_Box BOX_Action_Menu;
  private Button BTN_Drop;
  private Button BTN_Drop_All;
  private Button BTN_Use;
  public int index;
  public Inventory_System Inventory_System;

  ........
}
```

<summary>Event Graph</summary>

<ins>Construct</ins>

This Events allows for the menu to appear when the the mouse right click is done.

![imagen](https://github.com/user-attachments/assets/72ddc649-7c9c-42a6-9760-db2fe39ea935) [^73]

[^73]: Construct in Unreal Engine 5

```
public event Construct() {
  SetPosition(SlotasCanvasSlot(BOX_Action_Menu), GetMousePositionOnViewport());
}
```

<ins>OnMouseLeave</ins>

This Events allows for the menu to disappear when the player no,onger hovers the mouse over the menu

![imagen](https://github.com/user-attachments/assets/20da064f-ca4c-4ddc-92a7-a2f00d3e8be6) [^74]

[^74]: OnMouseLeave in Unreal Engine 5

```
public event OnMouseLeave() {
  RemoveFromParent(self);
}
```

<ins>OnClickedBTN_Drop</ins>

This Events remove an item in the slot from the inventory when clicking the "Drop" button

![imagen](https://github.com/user-attachments/assets/72224506-d9c8-42e0-b00b-8e28c2102060) [^75]

[^75]: OnClickedBTN_Drop in Unreal Engine 5

```
public event OnClickedBTN_Drop() {
  Server_Remove(Inventory_System, index, false, false);
}
```

<ins>OnClickedBTN_Drop_All</ins>

This Events remove all items in the slot from the inventory when clicking the "Drop All" button

![imagen](https://github.com/user-attachments/assets/1d435c6e-8f6a-4f83-bf45-df2480af72f0) [^76]

[^76]: OnClickedBTN_Drop_All in Unreal Engine 5

```
public event OnClickedBTN_Drop_All() {
  Server_Remove(Inventory_System, index, true, false);
}
```

<ins>OnClickedBTN_Use</ins>

This Events remove an item in the slot from the inventory when clicking the "Use" button, and consumes it, this gives the player an effect

![imagen](https://github.com/user-attachments/assets/360a8836-bfb4-4bd3-afdc-b2f226f4dfbb) [^78]

[^78]: OnClickedBTN_Use in Unreal Engine 5

```
public event OnClickedBTN_Use() {
  Consume_Item(Inventory_System, index);
}
```
</details>

<details>

<summary>W_Container_Inventory</summary>

# W_Container_Inventory

## What's in an Chest

This is a UI that creates the inventory of a container alongside the invenotry of the player.

### Widget Configuration

![imagen](https://github.com/user-attachments/assets/b473ac50-74c2-49a9-9b6c-039a6c68c832) [^79]

[^79]: W_Container_Inventory class diagram

![imagen](https://github.com/user-attachments/assets/7346c2b7-8b2b-4846-bed9-aae1f7cbdd2d) [^80]

[^80]: W_Container_Inventory designer in Unreal Engine 5

```
#import Inventory_System

public class W_Container_Inventory{

  private ContainerInventoryGrid W_Inventory_Grid;
  private PlayerInventoryGrid W_Inventory_Grid;
  public Inventory_System ContainerInventoryComp;
  ........
}
```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the inventory to be generated before during the project loading. It displays both the conainters inventory and the player's

![imagen](https://github.com/user-attachments/assets/7a7ed99b-6550-4121-bf38-e15a5d40c799) [^81]

[^81]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
  DisplayInventory(PlayerInventoryGrid, getComponentByClass(getPlayerCharacter(), "Inventory_System"));
  DisplayInventory(ContainerInventoryGrid, ContainerInventoryComp);
}
```

<ins>Construct</ins>

This Events allows for when the container inventory is show, it makes the game use mouse only.
![imagen](https://github.com/user-attachments/assets/bc8c2fd4-32fd-4e63-88ae-9da92ddd53d1) [^82]

[^82]: Construct in Unreal Engine 5

```
public event Construct() {
  setInpuModeUIOnly(getPlayerController(0), ContainerInventoryGrid, "Do Not Look", false);
  setMouseCursor(true, getPlayerController(0));
}
```

<ins>Destruct</ins>

This Events removes the Mouse only option from the project when the container inventory is removed

![imagen](https://github.com/user-attachments/assets/2193edcf-84d0-4d4b-8def-715a3d54c4d6) [^83]

[^83]: Destruct in Unreal Engine 5

```
public event Destruct() {
  if(isValid(getPlayerController(0))){
    setInputModeGameOnly(getPlayerController(0), false);
    setMouseCursor(false, getPlayerController(0));
  }
}
```
<summary>OnKeyDown</summary>

This is a function that closes the container's inventory when a specified button is pressed

![imagen](https://github.com/user-attachments/assets/9eee59e4-0079-4872-b6c3-314cc6bcc21b) [^84]

[^84]: OnKeyDown in Unreal Engine 5

```
public OnKeyDown(KeyEvent InKeyEvent){
  if(getKey(InKeyEvent)) == "E" || getKey(InKeyEvent)) == "Gamepad Face Button Right" || getKey(InKeyEvent)) == "Gamepad Special Right"){
    removeFromParent(self);
    return EventReply value = handled();
  }
}
```

</details>

<details>

<summary>W_Display_Message</summary>

# W_Display_Message

## Shwoing the item message

This is a UI that creates a message when the player is looking at an item that can be pick uped.

### Widget Configuration

![imagen](https://github.com/user-attachments/assets/73744899-6e70-4cd0-9420-908e4cb09900) [^85]

[^85]: W_Display_Message class diagram

![imagen](https://github.com/user-attachments/assets/cffb6007-768e-4f69-a9a4-755bd37e183d) [^86]

[^86]: W_Display_Message designer in Unreal Engine 5

```
public class W_Display_Message{

  private TextBox TXT_Message;
  private Border BOX_Meesage;
  private text message;
  ........
}
```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the message to be made, it is set as "No-Hit" as default so it will not show up at all times

![imagen](https://github.com/user-attachments/assets/9e184eb7-4487-4d65-8004-a22c25de41d5) [^87]

[^87]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
  setText(TXT_Message, message);
  if(!message.isEmpty()){
    setVisibility(self, "Not Hit-Testable");
  }else {
    setVisibility(self, "Hidden");
  }
}
```

<ins>Show_Message</ins>

This Events allows for message to be shown when an item is nearby.

![imagen](https://github.com/user-attachments/assets/a789d348-e497-4977-acbf-e783dc4be325) [^88]

[^88]: Show_Message in Unreal Engine 5

```
public event Show_Message(text message) {
  message = this.message;
    setText(TXT_Message, message);
  if(!message.isEmpty()){
    setVisibility(self, "Not Hit-Testable");
  }else {
    setVisibility(self, "Hidden");
  }
}
```
</details>

<details>

<summary>W_Drag_Preview</summary>

# W_Drag_Preview

## Showing a preview while dragging the mouse

This is a UI that creates a preview of the selected item when it is dragged with the mouse

### Widget Configuration

![imagen](https://github.com/user-attachments/assets/2f16256b-b2d7-4033-86c6-de74ba77f6fc) [^89]

[^89]: W_Drag_Preview class diagram

![imagen](https://github.com/user-attachments/assets/6316491b-3c2b-4521-8e63-89dd617f00bb) [^90]

[^90]: W_Drag_Preview designer in Unreal Engine 5

```
public class W_Drag_Preview{

  private Image Image32;
  public name itemID;
  ........
}
```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the preview to be made, it gets the specific item's data being seen.

![imagen](https://github.com/user-attachments/assets/9e184eb7-4487-4d65-8004-a22c25de41d5) [^91]

[^91]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
  setBrushFromTextyue(Image32, break(getTableFromRow(Item_Data, itemID)).thumbnail);
}
```

</details>

<details>

<summary>W_Hotbar</summary>

# W_Hotbar

## A Hotbar for pinning spcific items

This is a UI that creates a Hotbar for the player to pin certain items into the hotbar slots

### Widget Configuration

![imagen](https://github.com/user-attachments/assets/79a3a075-0441-4075-992a-7bf50af7c6d7) [^92]

[^92]: W_Hotbar class diagram

![imagen](https://github.com/user-attachments/assets/87352344-55eb-485b-8b93-3a9f05cc703b) [^93]

[^93]: W_Hotbar designer in Unreal Engine 5

```
public class W_Hotbar{

  private W_Hotbar_Slot W_Hotbar_Slot;
  private W_Hotbar_Slot W_Hotbar_Slot_1;
  private W_Hotbar_Slot W_Hotbar_Slot_2;
  private W_Hotbar_Slot W_Hotbar_Slot_3;
  private W_Hotbar_Slot W_Hotbar_Slot_4;
  private W_Hotbar_Slot W_Hotbar_Slot_5;
  private W_Hotbar_Slot W_Hotbar_Slot_6;
  private W_Hotbar_Slot W_Hotbar_Slot_7;
  
  ........
}
```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the Hotbar to be generated, and to load any saved hotbars from the save file, this also creates a new Event called "OnHotBarUpdated".

![imagen](https://github.com/user-attachments/assets/e23e4cd4-9b47-45dd-aea6-14b826f9aea0) [^94]

[^94]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
  BindEvent(/*Event=*/ CreateEvent(self, "OnHotBarUpdated(slot)"),
            /*Targets*/ W_Hotbar_Slot,
            W_Hotbar_Slot_1,
            W_Hotbar_Slot_2,
            W_Hotbar_Slot_3,
            W_Hotbar_Slot_4,
            W_Hotbar_Slot_5,
            W_Hotbar_Slot_6,
            W_Hotbar_Slot_7);
  delay(1.0);
  LoadHotbar();
}
```


<ins>OnHotbarUpdated</ins>

This Events is active when any change is done in a slot of the hotbar, it also automatically saves the current hotbar when it is updated.

![imagen](https://github.com/user-attachments/assets/9041e5a9-bd6f-4113-a68a-748dcd3be417) [^95]

[^95]: OnHotbarUpdated in Unreal Engine 5

```
public event OnHotBarUpdated(W_Hotbar_Slot slot) {
   forEach x in MakeArray( W_Hotbar_Slot, W_Hotbar_Slot_1, W_Hotbar_Slot_2, W_Hotbar_Slot_3, W_Hotbar_Slot_4, W_Hotbar_Slot_5, W_Hotbar_Slot_6, W_Hotbar_Slot_7){
    if(x.inventoryIndex == slot.inventoryIndex AND x != slot){
      slot.inventoryIndex = -1;
      OnHotbarUpdated(slot);
    }
  }
  SaveHotbar();
}
```

<summary>SaveHotbar</summary>

This function saves the current Hotbar slots so it will load on the next load.

![imagen](https://github.com/user-attachments/assets/da1023df-448e-441d-a986-5a14201d000b) [^96]

[^96]: SaveHotbar function in Unreal Engine 5

```

public SaveHotbar(){
  int[] localHotbarContent;
  forEach x in MakeArray( W_Hotbar_Slot, W_Hotbar_Slot_1, W_Hotbar_Slot_2, W_Hotbar_Slot_3, W_Hotbar_Slot_4, W_Hotbar_Slot_5, W_Hotbar_Slot_6, W_Hotbar_Slot_7){
    setArrayElem(localHotbarElem, x.index, x.inventoryIndex, true);
  }
  ((My_Game_Instance) GetGameInstance()).SaveData-PlayerData.hotbarContent = localHotbarContent;
  SaveGameToSlot(((My_Game_Instance) GetGameInstance()).SaveData-PlayerData, "MyData", 0);
}

```

<summary>LoadHotbar</summary>

This function loads the saved Hotbar slots at the beginning at the project.

![imagen](https://github.com/user-attachments/assets/100e28ae-b833-49d8-9e78-3b9d7eb6af68) [^97]

[^97]: LoadHotbar function in Unreal Engine 5

```

public SaveHotbar(){
  SaveData-PlayerData localSaveData = ((My_Game_Instance) GetGameInstance()).SaveData-PlayerData;
  forEach x in MakeArray( W_Hotbar_Slot, W_Hotbar_Slot_1, W_Hotbar_Slot_2, W_Hotbar_Slot_3, W_Hotbar_Slot_4, W_Hotbar_Slot_5, W_Hotbar_Slot_6, W_Hotbar_Slot_7){
    localSaveData.hotbarContent[find(x.index)] = x;
    OnUpdateHotBarSlot(x);
  }
}

```
</details>

<details>

<summary>W_Hotbar_Slot</summary>

# W_Hotbar_Slot

## A Hotbar slot for the hotbar

This is a UI that creates a Hotbar slot for the hotbar, it shows an item in a secific slot in the inventory.

### Widget Configuration

![imagen](https://github.com/user-attachments/assets/94e179d1-8c7a-41e6-9b49-4c22beb34e56) [^98]

[^98]: W_Hotbar_Slot class diagram

![imagen](https://github.com/user-attachments/assets/6e1c062d-bb20-47cd-9a2c-08ed063b61fe) [^99]

[^99]: W_Hotbar_Slot designer in Unreal Engine 5

```
#import W_Action_Menu
#import W_Drag_Preview
#import DD_Inventory_Slot

public class W_Hotbar_Slot{

  public name itemID;
  public int quantity;
  public int contentIndex;
  private W_Action_Menu Action_Menu;
  public int inventoryIndex;
  private SizeBox BOX_Quantity;
  private Border BRD_Border;
  private Button BTN_SlotButton;
  private Image IMG_Icon;
  private TextBlock TXT_Quantity;
  private EventDispatcher OnHotbarUpdated();
  ........
}
```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the Hotbar Slot to be generated, it will generate any pinned item from the inventory

![imagen](https://github.com/user-attachments/assets/21d7fb0c-93bb-49ef-a327-423a0317607f) [^100]

[^100]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
  InventoryComponent = GetComponentByClass(GetPlayerController(0), "Inventory_System");
  BindEventToOnInventoryUpdate(InventoryComponent, OnUpdateHotBarSlot());
  if(InventoryComponent.content.isValidIndex(inventoryIndex)){
    itemID = InventoryComponent.content[inventroyIndex].itemName;
    quantity = InventoryComponent.content[inventoryIndex].quantity;
    switch(GetDataTableRow("Item_Data", itemID)){
      case "Row Found":
        SetBrushfromTexture(IMG_Icon, BreakF_Item_Struct(GetDataTableRow("Item_Data", itemID)).thumbnail, false);
        SetText(TXT_Quantity. quantity.Totext());
        SetVisibility(BOX_Quantity, IMG_Icon, "Visible");
      case "Row Not Found":
        SetVisibility(BOX_Quantity, IMG_Icon, "Hidden");
    }
  } else {
    SetVisibility(BOX_Quantity, IMG_Icon, "Hidden");
  }
}
```


<ins>OnUpdateHotBarSlot</ins>

This Events is active when any change is done in a slot of the hotbar slot.

![imagen](https://github.com/user-attachments/assets/41914d1d-2ad1-4c73-a32d-404cfe647334) [^101]

[^101]: OnHotbarUpdated in Unreal Engine 5

```
public event OnHotBarSlotUpdated() {
   if(InventoryComponent.content.isValidIndex(inventoryIndex)){
    itemID = InventoryComponent.content[inventroyIndex].itemName;
    quantity = InventoryComponent.content[inventoryIndex].quantity;
    switch(GetDataTableRow("Item_Data", itemID)){
      case "Row Found":
        SetBrushfromTexture(IMG_Icon, BreakF_Item_Struct(GetDataTableRow("Item_Data", itemID)).thumbnail, false);
        SetText(TXT_Quantity. quantity.Totext());
        SetVisibility(BOX_Quantity, IMG_Icon, "Visible");
      case "Row Not Found":
        SetVisibility(BOX_Quantity, IMG_Icon, "Hidden");
    }
  } else {
    SetVisibility(BOX_Quantity, IMG_Icon, "Hidden");
  }
}
```

<summary>OnPreviewMouseButtonDown</summary>

This function show the Drag and Drop preview when the mouse button is being hold

![imagen](https://github.com/user-attachments/assets/b71cb879-2e4c-4f80-95fd-f3bc5ae6c917) [^102]

[^102]: OnPreviewMouseButtonDown function in Unreal Engine 5

```

public OnPreviewMouseButtonDown(PointerEvent MouseEvent){
  if(itemID == ""){
    if(IsMouseButtonDown(MouseEvent, "Left Mouse Button")){
      return DetectDragIfPressed(MouseEvent, "Left Mouse Button");
    } else {
      if(IsMouseButtonDown(MouseEvent, "Right Mouse Button")){
        if(Action_Menu.isValid()){
          RemoveFromParent(Action_Menu);
          Action_Menu = CreateWidget("W_Action_Menu", InventoryContent, contentIndex);
          AddtoViewport(Action_Menu);
        } else {
          Action_Menu = CreateWidget("W_Action_Menu", InventoryContent, contentIndex);
          AddtoViewport(Action_Menu);
        }
      }
    }
  } else {
    return ReplyStructure value = Unhandled()
  }
}

```

<summary>OnDragDetected</summary>

This function creates the Drag and Drop functionality;

![imagen](https://github.com/user-attachments/assets/423632a5-0739-457c-97cd-afbc713cf749) [^103]

[^103]: OnDragDetected function in Unreal Engine 5

```

public OnDragDetected(){
  return Create(/*Class*/ "DD_Inventory_Slot",
              /*Default Drag Visual*/ CreateWidget("W_Drag_Preview",null, itemID),
              /*Pivot*/ "Center Center",
              /*Inventory*/ InventoryComponent,
              /*Content Index*/ invenotryIndex);
}

```

<summary>OnDrop</summary>

This function detects when an item is dragged and dropped into the slot;

![imagen](https://github.com/user-attachments/assets/074708d2-9cd6-40f7-8506-746a3ba8a6a1) [^104]

[^104]: OnDrop function in Unreal Engine 5

```

public OnDrop(DragDrop operation){
    inventoryIndex = ((DD_Inventory_Slot) operation).contentIndex;
    OnUpdateHotBarSlot();
    CallOnHotbarUpdated(self, /*slot*/ self);
    return true;
}

```
</details>

<details>

<summary>W_Inventory_Button</summary>

# W_Inventory_Button

## Let's filter our items

This is a UI that creates a Button that has a specific item, this button will filter out that type that item.

### Widget Configuration

<img width="517" height="487" alt="imagen" src="https://github.com/user-attachments/assets/bb70c399-3a93-4dbd-981f-154ea8687a64" /> [^105]

[^105]: W_Inventory_Button class diagram

<img width="1409" height="855" alt="imagen" src="https://github.com/user-attachments/assets/f0b19dae-4938-4d68-a777-0343c7bfd40c" /> [^106]

[^106]: W_Inventory_Button designer in Unreal Engine 5

```

public class W_Inventory_Button{
  private Button BTN_Button;
  private TextBlock TXT_buttonText;
  public Text buttonText;
  private boolean isOn;
  public int filterFlag;
  private EventDispatcher OnButtonCLicked();
  ........
}
```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the button to be generated, it will generate the text and put it on the button
<img width="736" height="408" alt="imagen" src="https://github.com/user-attachments/assets/d6594fa7-218a-43d5-9926-e928bb4e515e" /> [^107]

[^107]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
  set(buttonText, TXT_buttonText);
}
```


<ins>OnClickedBTN_Button</ins>

This Events is active whenthe button is pressed, it will activate the filter, and change its color

<img width="1120" height="337" alt="imagen" src="https://github.com/user-attachments/assets/9183910e-050f-4559-b1be-844d3a855e2b" /> [^108]

[^108]: OnClickedBTN_Button in Unreal Engine 5

```
public event OnClickBTN_Button() {
  isON = !isOn;
  SetBackgroundColor(BTN_Button, selectColor("white", "gray", isOn));
  SetColorandOpacitiy(TXT_buttonText, MakeSlateColor(selectColor("gray", "white", isOn), "Specified Color"));
  CallOnButtonClicked(self, isOn, filterFlag);
}
```

</details>

<details>

<summary>W_Inventory_Grid</summary>

# W_Inventory_Grid

## Where we can see out items

This is a UI that creates the grid with all of our items slots.

### Widget Configuration

<img width="519" height="569" alt="imagen" src="https://github.com/user-attachments/assets/65e396f6-1576-4627-94b4-876dee688b05" /> [^109]

[^109]: W_Inventory_Grid class diagram

<img width="1407" height="846" alt="imagen" src="https://github.com/user-attachments/assets/3a4c0f5f-c3d9-44e6-bdcc-e6e38a0c9fc3" /> [^110]

[^110]: W_Inventory_Grid designer in Unreal Engine 5

```
#import Inventory_System

public class W_Inventory_Grid{
  private int currentFilter;
  private boolean isFilterActive;
  private WrapBox BOX_Grid;
  private W_Inventory_Button W_Inventroy_Button_36;
  private W_Inventory_Button W_Inventroy_Button_1;
  private W_Inventory_Button W_Inventroy_Button_2;
  private W_Inventory_Button W_Inventroy_Button_3;
  private W_Inventory_Button W_Inventroy_Button_4;
  public InventorySystem InventorySystem;

  ........
}
```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the grid to be generated, it will check for any filter4s being active
<img width="736" height="408" alt="imagen" src="https://github.com/user-attachments/assets/d6594fa7-218a-43d5-9926-e928bb4e515e" /> [^111]

[^111]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
  if (InventorySystem.isValid()){
    forEach x in InvenotrySystem.content {
      if(FilterItem(content[x]) OR content[x].itemName == ""){
        AddChild(BOX_Grid, createWidget("W_Inventory_Slot", content[x].itemName, content[x].quantity, InventorySystem, x.index));
      }
    }
    BindEvent(InventorySystem, CreateEvent("Update_Inventory()"));
  }
}
```


<ins>DisplayInventory</ins>

This Events is active when the player opens the inventory, it is extremely similar to Preconstruct, it sets the current inventroy System, and clears any unwanted children.

<img width="1548" height="518" alt="imagen" src="https://github.com/user-attachments/assets/324064e4-84ca-4ec8-8f27-f96cd161575d" /> [^112]

[^112]: DisplayInventory_Button in Unreal Engine 5

```
public event DisplayInventory(InventorySystem InventorySystem) {
  InventorySystem = this.InventorySystem;
  ClearChildren(BOX_Grid);
  if (InventorySystem.isValid()){
    forEach x in InvenotrySystem.content {
      if(FilterItem(content[x]) OR content[x].itemName == ""){
        AddChild(BOX_Grid, createWidget("W_Inventory_Slot", content[x].itemName, content[x].quantity, InventorySystem, x.index));
      }
    }
    BindEvent(InventorySystem, CreateEvent("Update_Inventory()"));
  }
}
```

<ins>UpdateInventory</ins>

This Events activates and updates the inventory, it is extremely similar to Preconstruct, it clears any unwanted children and sets the new inventory.

<img width="1535" height="543" alt="imagen" src="https://github.com/user-attachments/assets/d97a7d29-4183-4e4f-adc6-a5076e230f59" /> [^113]

[^113]: UpdateInventory in Unreal Engine 5

```
public event UpdateInventory() {
  ClearChildren(BOX_Grid);
  if (InventorySystem.isValid()){
    forEach x in InvenotrySystem.content {
      if(FilterItem(content[x]) OR content[x].itemName == ""){
        AddChild(BOX_Grid, createWidget("W_Inventory_Slot", content[x].itemName, content[x].quantity, InventorySystem, x.index));
      }
    }
    BindEvent(InventorySystem, CreateEvent("Update_Inventory()"));
  }
}
```


<ins>Construct</ins>

This Events is active when a Filter is changed, this will show change the grid to only show the items specified by the filter.

<img width="1357" height="434" alt="imagen" src="https://github.com/user-attachments/assets/05842e2c-2163-4e25-88ab-5d9fca1ddecb" /> [^114]

[^114]: Construct in Unreal Engine 5

```
public event Construct() {
  forEach x in MakeArray(W_Inventory_Button_1, W_Inventory_Button_2, W_Inventory_Button_3, W_Inventory_Button_4, W_Inventory_Button_36){
    BindEventToOnButtonClicked(x, "OnFilterChange()");
  }
}
```

<ins>OnFilterChange</ins>

This Events is generates the inventory grid and binds the buttons to the filter change event.

<img width="1073" height="224" alt="imagen" src="https://github.com/user-attachments/assets/8316027f-3e35-4b93-a2ee-7c03cced5b7f" /> [^115]

[^115]: OnFilterChange in Unreal Engine 5

```
public event OnFilterChange(boolean isON, int filter) {
  currentFilter = selectInt((filter | currentFilter), (filter ^ currentFIlter), isOn);
  if((float) currentFilter == 0.0){
    isFilterActive = false;
    UpdateInventory();
  } else {
    isFilterActive = true;
    UpdateInventroy();
  }
}
```
<summary>FilterItem</summary>

This function allows for the grid to add a filter, the filter i based on the category of the item, this is changed into a bitmask, and returns the filter.

<img width="1277" height="527" alt="imagen" src="https://github.com/user-attachments/assets/17b1a4a0-6a61-4da0-8396-1a2b6c144269" /> [^116]

[^116]: FilterItem Function in Unreal Engine 5

```
public FilterItem(F_Slot_Struct slotName) {
  switch(OutRow as GetDataTableRow("Item_Data", slotName.itemName)){
    case "Row Found":
      if(isFilterActive){
        return (currentFilter & MakeBitMask(Truncate(Power(2.0, (Float) (Int) OutRow.Category)))) ==  MakeBitMask(Truncate(Power(2.0, (Float) (Int) OutRow.Category)));
      } else {
        return false;
      }
  }
}
```

</details>

<details>

<summary>W_Inventory_Slot</summary>

# W_Inventoryr_Slot

## Our Items in their own slot

This is a UI that creates an Inventory slot for the Inventory Grid, it shows an item type that the player has picked up.

### Widget Configuration

<img width="618" height="425" alt="imagen" src="https://github.com/user-attachments/assets/5bef1569-3464-43aa-b030-af520bdcbe84" /> [^117]

[^117]: W_Inventory_Slot class diagram

<img width="1424" height="864" alt="imagen" src="https://github.com/user-attachments/assets/1fab501d-7421-47e7-b9e3-53ec75abeff3" /> [^118]

[^118]: W_Inventory_Slot designer in Unreal Engine 5

```
#import W_Action_Menu
#import W_Drag_Preview
#import DD_Inventory_Slot

public class W_Inventory_Slot{

  public name itemID;
  public int quantity;
  public int contentIndex;
  private W_Action_Menu Action_Menu;
  public int inventoryIndex;
  private SizeBox BOX_Quantity;
  private Border BRD_Border;
  private Button BTN_SlotButton;
  private Image IMG_Icon;
  private TextBlock TXT_Quantity;
  ........
}
```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the Inventory Slot to be generated, it will generate any picked uped item.

<img width="1403" height="616" alt="imagen" src="https://github.com/user-attachments/assets/2bcc2c57-f595-4cc4-a952-921af22161b3" /> [^119]

[^119]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
    switch(GetDataTableRow("Item_Data", itemID)){
      case "Row Found":
        SetBrushfromTexture(IMG_Icon, BreakF_Item_Struct(GetDataTableRow("Item_Data", itemID)).thumbnail, false);
        SetText(TXT_Quantity. quantity.Totext());
        SetVisibility(BOX_Quantity, IMG_Icon, "Visible");
      case "Row Not Found":
        SetVisibility(BOX_Quantity, IMG_Icon, "Hidden");
    }
}
```

<summary>OnPreviewMouseButtonDown</summary>

This function show the Drag and Drop preview when the mouse button is being hold

<img width="1464" height="383" alt="imagen" src="https://github.com/user-attachments/assets/b2dbf0c3-f265-4bc0-afc9-72741b2364f1" /> [^120]

[^120]: OnPreviewMouseButtonDown function in Unreal Engine 5

```

public OnPreviewMouseButtonDown(PointerEvent MouseEvent){
  if(itemID == ""){
    if(IsMouseButtonDown(MouseEvent, "Left Mouse Button")){
      return DetectDragIfPressed(MouseEvent, "Left Mouse Button");
    } else {
      if(IsMouseButtonDown(MouseEvent, "Right Mouse Button")){
        if(Action_Menu.isValid()){
          RemoveFromParent(Action_Menu);
          Action_Menu = CreateWidget("W_Action_Menu", InventoryContent, contentIndex);
          AddtoViewport(Action_Menu);
        } else {
          Action_Menu = CreateWidget("W_Action_Menu", InventoryContent, contentIndex);
          AddtoViewport(Action_Menu);
        }
      }
    }
  } else {
    return ReplyStructure value = Unhandled()
  }
}

```

<summary>OnDragDetected</summary>

This function creates the Drag and Drop functionality;

<img width="1284" height="425" alt="imagen" src="https://github.com/user-attachments/assets/bfa4e922-aa80-4c83-9c88-e27c251d82a6" /> [^121]

[^121]: OnDragDetected function in Unreal Engine 5

```

public OnDragDetected(){
  return Create(/*Class*/ "DD_Inventory_Slot",
              /*Default Drag Visual*/ CreateWidget("W_Drag_Preview",null, itemID),
              /*Pivot*/ "Center Center",
              /*Inventory*/ InventoryComponent,
              /*Content Index*/ invenotryIndex);
}

```

<summary>OnDrop</summary>

This function detects when an item is dragged and dropped into the slot;

<img width="1475" height="483" alt="imagen" src="https://github.com/user-attachments/assets/a9581d6b-8556-44dd-abeb-feee3d8d92a7" /> [^122]

[^122]: OnDrop function in Unreal Engine 5

```

public OnDrop(DragDrop operation){
  if(((DD_Inventory_Slot) operation).contentIndex != contentIndex OR ((DD_Inventory_Slot) operation).inventory != inventoryComponent){
    Server_Transfer_Slots(InventoryComponent, ((DD_Inventory_Slot) operation).contentIndex, ((DD_Inventory_Slot) operation).inventory, contentIndex);
    return true;
  }
}

```
</details>

<details>

<summary>W_Player_Menu</summary>

# W_Player_Menu

## What the player sees

This is a UI that creates the Player's Menu

### Widget Configuration

<img width="1419" height="867" alt="imagen" src="https://github.com/user-attachments/assets/003bbaab-6c3c-4d04-bc64-afbd78147e4c" /> [^123]

[^123]: W_Player_Menu class diagram

<img width="309" height="224" alt="imagen" src="https://github.com/user-attachments/assets/06ae444b-dcd6-459f-adbf-c8c9b0e8b63f" /> [^124]

[^124]: W_Player_Menu designer in Unreal Engine 5

```
#import W_Inventory_Grid

public class W_Player_Menu{

  private W_Inventory_Grid W_Inventory_Grid
  ........
}

```

<summary>Event Graph</summary>

<ins>PreConstruct</ins>

This Events allows for the Player Menu to be generated and display the inventory

<img width="1310" height="491" alt="imagen" src="https://github.com/user-attachments/assets/df23f7e4-5e5c-4615-a77e-bcd2ccc3f8f7" /> [^125]

[^125]: PreConstruct in Unreal Engine 5

```
public event PreConstruct() {
  DisplayInevntory(W_Inventory_Grid, GetComponentByClass(GetPlayerCharacter(0)), "Inventory System"));
}
```

<ins>Construct</ins>

This Events allows for the Player Menu to be mouse use only

<img width="1539" height="450" alt="imagen" src="https://github.com/user-attachments/assets/3bbc8745-ff6c-4015-86a4-25f266682fd0" /> [^126]

[^126]: Construct in Unreal Engine 5

```
public event Construct() {
  SetInputModeUIOnly(GetPlayerController(), W_Inventory_Grid, "Do not look", false);
  SetShowMouseCursor(true, GetPlayerController(0));
}
```

<ins>Destruct</ins>

This Events allows for the Player Menu to be destroyed and hides the mouse

<img width="1495" height="396" alt="imagen" src="https://github.com/user-attachments/assets/b2c25778-6527-46d4-a04d-8924fb2bff92" /> [^127]

[^127]: Destruct in Unreal Engine 5

```
public event Destruct() {
  if(GetPLayerController(0).isValid){
    SetInputModeGameOnly(GetPlayerController(0), false);
    SetShowMouseCursor(false, GetPlayerController(0));
  }
}
```


<summary>OnKeyDown</summary>

This function allows the player to press the I key to open the inventory.

<img width="1464" height="383" alt="imagen" src="https://github.com/user-attachments/assets/b2dbf0c3-f265-4bc0-afc9-72741b2364f1" /> [^128]

[^128]: OnKeyDown function in Unreal Engine 5

```

public OnKeyDown(KeyEvent InKeyEvent){
  if(getKey(InKeyEvent) == "I" OR getKey(InKeyEvent) == "Gamepad Special Right"){
    RemoveFromParent();
    return Unhandled();
  }
}

```
</details>

<details>

<summary>F_Container_Struct</summary>

# F_Container_Struct

## How we handle Containers

This is a structure that allows the Inventory System to manage the position and slots of every container.

### Structure Elements

<img width="377" height="219" alt="imagen" src="https://github.com/user-attachments/assets/9d6e1016-5fcc-4926-97d0-054fd7443e32" /> [^129]

[^129]: F_Container_Struct Class Diagram

<img width="1920" height="454" alt="imagen" src="https://github.com/user-attachments/assets/f7a9c9d1-b0d5-43ef-97ab-3bbbcdaa7536" /> [^130]

[^130]: F_Container_Struct in Unreal Engine 5



```
#import F_Slot_Struct

struct F_Container_Struct{
  F_Slot_Struct[] contents,
  Transform transform;
}

```
  
</details>

<details>

<summary>My_Game_Instance</summary>

# My_Game_Instance

## Let's save our game

This is a blueprint class that allows the game instance to be saved, this will allow the player to save their inventory, conatiner inventories to be saved and loaded from instance.

### Structure Elements

<img width="455" height="205" alt="imagen" src="https://github.com/user-attachments/assets/daa4fd2d-01cc-4380-a6d6-7e4e7efa795a" /> [^131]

[^131]: My__Game_Instance Class Diagram


```
#import SaveData-PlayerData

public class My_Game_Instance{

  private SaveData-PlayerData asSaveData-PlayerData;

  .........
}

```

<summary>Event Graph</summary>

<ins>Init</ins>

This Event is at the beginning of the project, it checks for a save file, and creates or loads the intended save file for the player.

<img width="1704" height="377" alt="imagen" src="https://github.com/user-attachments/assets/e91fdd97-2adf-4138-be1b-fd013e9cce82" /> [^132]

[^132]: Init in Unreal Engine 5

```

public Init(){
  if(DoesSaveGameExist("MyData", 0)){
    try{
      asSaveData-PlayerData = (SaveData-PlayerData) LoadGamefromSlot("MyData", 0);
    }
  } else {
    asSaveData-PlayerData = CreateSaveGameObject("SaveData.PlayerData");
  }
}

```
</details>


<details>

<summary>SaveData-Level</summary>

# My_Game_Instance

## Let's save our game

This is a blueprint class that allows the game instance to be saved, this will allow the player to save their inventory, conatiner inventories to be saved and loaded from instance.

### Structure Elements

<img width="455" height="205" alt="imagen" src="https://github.com/user-attachments/assets/daa4fd2d-01cc-4380-a6d6-7e4e7efa795a" /> [^131]

[^131]: My__Game_Instance Class Diagram


```
#import SaveData-PlayerData

public class My_Game_Instance{

  private SaveData-PlayerData asSaveData-PlayerData;

  .........
}

```


```
</details>

