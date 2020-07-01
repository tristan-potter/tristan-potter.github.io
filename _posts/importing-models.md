---
layout: post
title:  "Game Engine - Importing Models" 
date:   2017-12-18 17:56:40 -0400
categories:
excerpt:
hero-image:
status: draft
author: tristan

---

Have a model struct that is used for rendering:


```go
type Mesh struct {
  name:     string,
  mesh:     uint32,
}
  
type Texture struct {
  name:     string,
  texture:  uint32,
}
    
type Specular struct {
  name:          string,
  shine:         float32,
  reflectivity:  float32,
}

type BasicModel{
  mesh:     Mesh,
  texture:  Texture,
  specular: Specular,
  shader:   uint32, // from shaderLibrary
  ...other details like uniforms
}

func NewBasicModel(mesh, texture, specular) {
  return Model{}
}

func (b BasicModel) Instantiate(dispatch) {
  // 
}
func (m Model) Render(view, lights, transforms) { 
  m.render(view, lights transforms) 
}

type Shader struct {
  //stuff
}

func (shader) Render(view, lights, mesh, texture, specularity, transforms) {

}
```

Can use another struct to keep track of all Models:

```
type ModelLibrary struct {
  models   []Model
  modelMap map[string]uint32
}

func NewModelLibrary(models []Model) {

}
```

Shaders, create each shader, send the slice of shaders to a NewShaderLibrary 
function that returns a ShaderLibrary  that users can use to find out the shader
id.

Models get added to the map by name, so that they can be easily linked to
entities in the components.

Registry will be a component registry, shaders will be in a library.

Need to seperate mesh, texture, and other stats (like reflectivity and shine) so
that they can be linked to seperately. The collection of these things makes a
model, which will be added using a \"Model.Instantiate()\" function that sends the
relevent actions and returns an entity ID.

ModelLibrary => MeshLibrary, TextureLibrary, SpecularLibrary
