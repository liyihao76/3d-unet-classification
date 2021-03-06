
get_training_and_validation_generators
=================================
<!-- TOC -->

- [get_validation_split](#get_validation_split)
- [data_generator](#data_generator)
  - [create_patch_index_list](#create_patch_index_list)
    - [compute_patch_indices](#compute_patch_indices)
      - [get_set_of_patch_indices](#get_set_of_patch_indices)

<!-- /TOC -->
```
train_generator, validation_generator, n_train_steps, n_validation_steps = get_training_and_validation_generators
```
Générer des patch pour l'ensemble d'entraînement et l'ensemble de vérification, generer steps_per_epoch et validation_steps

# get_validation_split
```
training_list, validation_list = get_validation_split
```
Diviser l'ensemble de formation et la liste des ensembles de vérification  
J'ai un total de 30 ensembles de données d'image, l'ensemble d'entraînement occupe 80%  
30*0.8=24  

# data_generator
```
 training_generator = data_generator
 validation_generator = data_generator
```
définition:
```
Partie 1: Générer la liste de index pour les patchs dans 3d volume

index_list = create_patch_index_list  #27*24
```
```
Partie 2: obtenir des données des patchs

while len(index_list) > 0: #648 647 646 ......1  0
           index = index_list.pop()
           add_data
```
```
Partie 3: generer steps_per_epoch et validation_steps
get_number_of_steps
get_number_of_patches
```

## create_patch_index_list
Renvoie l'index de tous les patches dans les données d'image 3D
```
patch_index = list()
for index in training_list: # 1-24
    patches = compute_patch_indices # Un ensemble de données a 27 points
    patch_index.extend(itertools.product([index], patches)) # 24*27=648 index dans l'ensemble d'entraînement
return patch_index
 ```

### compute_patch_indices
Calculer une liste d'index dans un volume 3D  

image_shape=(144,144,144)    
patch_size=(64,64,64)  
nombre_patch=(144,144,144) / (64,64,64) = [3. 3. 3.]  
overflow = (patch_size) * n_patches - image_shape = [48. 48. 48.]  
start = -np.ceil(overflow/2)=[-24. -24. -24.]  
stop = image_shape   
step = patch_size     
return get_set_of_patch_indices(start, stop, step)


#### get_set_of_patch_indices
```
return np.asarray(np.mgrid[start[0]:stop[0]:step[0], start[1]:stop[1]:step[1],
                               start[2]:stop[2]:step[2]].reshape(3, -1).T, dtype=np.int)
```
[[-24 -24 -24]
 [-24 -24  40]
 [-24 -24 104]
 [-24  40 -24]
 [-24  40  40]
 [-24  40 104]
 [-24 104 -24]
 [-24 104  40]
 [-24 104 104]
 [ 40 -24 -24]
 [ 40 -24  40]
 [ 40 -24 104]
 [ 40  40 -24]
 [ 40  40  40]
 [ 40  40 104]
 [ 40 104 -24]
 [ 40 104  40]
 [ 40 104 104]
 [104 -24 -24]
 [104 -24  40]
 [104 -24 104]
 [104  40 -24]
 [104  40  40]
 [104  40 104]
 [104 104 -24]
 [104 104  40]
 [104 104 104]]

shape = (27, 3)
Un volume (144,144,144) a 27 patches   
Sa structure est un peu comme un Rubik's Cube
