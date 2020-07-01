---
layout: post
title:  "Fluorine Rendering"
date:   2017-12-18 17:56:40 -0400
categories:
excerpt:
hero-image:
status: draft
author: tristan

---

[intro]


--------------------------------------------------------------------------------

Heap vs Staack
Turns out the Golang GC is optimized to prevent pauses, so maybe heap things are
fine and we don't need to make our own GC.

Use recycling memory
[https://blog.cloudflare.com/recycling-memory-buffers-in-go/]  to make memory
operations more efficient.

Memory - Render loop
Use an array of Model  to track the main static rendering information (things
that don't change between instances). A seperate datastructure will keep track
of model_id  and an array of transforms that are currently in the scene. The
available models and shaders is static, and defined when the engine starts.

```go
type RenderModel struct {
  model_id uint32
  transforms []transformMatrix
}
```

The rendering method can then loop through, find the relevent model, call the
Render method on it, and pass in the transforms. This array of RenderModels that
the render engine will loop through will be created every 0.00833 seconds (8.33
ms), likely through looping through and finding the relevent model entities in
state (so only re-writing the transforms array of each element). If no changes,
keep underlying array the same (maybe store updated_at  in state?).

I think that this system might fix the issue of static vs dynamic models, since
static ones will just stick around in the render queue forever (until they are
removed).
https://stackoverflow.com/questions/12277426/how-to-minimize-the-garbage-collection-in-go
https://blog.golang.org/profiling-go-programs

Note that channels are very slow compared to slices / arrays.

Slices: Performance through cache friendliness  may be a good reference on
performance of slices.

Might be interesting: GigaVoxels: A Voxel-Based Rendering Pipeline For
Efficient
Exploration Of Large And Detailed Scenes
[https://maverick.inria.fr/Membres/Cyril.Crassin/thesis/CCrassinThesis_EN_Web.pdf]

State
Keep everything in state, but not all of it in memory. Store things to disk that
aren't being used (un-loaded chunks).

The difference between different entities is really in the priority of the
actions they create. Should probably create multiple dispatch lines so that
priority actions (input, movement, gravity) get handled at every render tick.
Non-priority actions (smelting, machinery, etc) can get handled every second.
Can likely cache and batch actions that are occuring outside of the chunk
(active actions only, if an animal moves into a chunk that's not loaded we don't
care what it does).

Things that \"grow\" or progress will be based on a function of in-world time, so
time outside of a loaded chunk will not affect growth rates. Interestingly, this
allows for multiple-dimensions time-streams, where spending n minutes in one
dimension might be equivalent to 10n minutes in another.
