# Glide3.7 load方法解析

## load

* RequestManager.load


```java
 public DrawableTypeRequest<String> load(String string) {
    return (DrawableTypeRequest<String>) fromString().load(string);
}

//生成DrawableTypeRequest。图片类型的请求
public DrawableTypeRequest<String> fromString() {
    return loadGeneric(String.class);
}
```

## RequestManger.loadGeneric


```java
 private <T> DrawableTypeRequest<T> loadGeneric(Class<T> modelClass) {
    ModelLoader<T, InputStream> streamModelLoader = Glide.buildStreamModelLoader(modelClass, context);
    ModelLoader<T, ParcelFileDescriptor> fileDescriptorModelLoader =
            Glide.buildFileDescriptorModelLoader(modelClass, context);
    if (modelClass != null && streamModelLoader == null && fileDescriptorModelLoader == null) {
        throw new IllegalArgumentException("Unknown type " + modelClass + ". You must provide a Model of a type for"
                + " which there is a registered ModelLoader, if you are using a custom model, you must first call"
                + " Glide#register with a ModelLoaderFactory for your custom model class");
    }

    return optionsApplier.apply(
            new DrawableTypeRequest<T>(modelClass, streamModelLoader, fileDescriptorModelLoader, context,
                    glide, requestTracker, lifecycle, optionsApplier));
}
```


## Glide.buildStreamModelLoader


```java
public static <T> ModelLoader<T, InputStream> buildStreamModelLoader(Class<T> modelClass, Context context) {
    return buildModelLoader(modelClass, InputStream.class, context);
}

 public static <T, Y> ModelLoader<T, Y> buildModelLoader(Class<T> modelClass, Class<Y> resourceClass,
            Context context) {
         if (modelClass == null) {
            if (Log.isLoggable(TAG, Log.DEBUG)) {
                Log.d(TAG, "Unable to load null model, setting placeholder only");
            }
            return null;
        }
        return Glide.get(context).getLoaderFactory().buildModelLoader(modelClass, resourceClass);
    }

```

## GenericLoaderFactory.buildModelLoader


```java
 public synchronized <T, Y> ModelLoader<T, Y> buildModelLoader(Class<T> modelClass, Class<Y> resourceClass) {
    ModelLoader<T, Y> result = getCachedLoader(modelClass, resourceClass);
    if (result != null) {
        // We've already tried to create a model loader and can't with the currently registered set of factories,
        // but we can't use null to demonstrate that failure because model loaders that haven't been requested
        // yet will be null in the cache. To avoid this, we use a special signal model loader.
        if (NULL_MODEL_LOADER.equals(result)) {
            return null;
        } else {
            return result;
        }
    }

    final ModelLoaderFactory<T, Y> factory = getFactory(modelClass, resourceClass);
    if (factory != null) {
        result = factory.build(context, this);
        cacheModelLoader(modelClass, resourceClass, result);
    } else {
        // We can't generate a model loader for the given arguments with the currently registered set of factories.
        cacheNullLoader(modelClass, resourceClass);
    }
    return result;
}

private <T, Y> ModelLoader<T, Y> getCachedLoader(Class<T> modelClass, Class<Y> resourceClass) {
    Map<Class/*Y*/, ModelLoader/*T, Y*/> resourceToLoaders = cachedModelLoaders.get(modelClass);
    ModelLoader/*T, Y*/ result = null;
    if (resourceToLoaders != null) {
        result = resourceToLoaders.get(resourceClass);
    }

    return result;
}
```

## DrawableTypeRequest


```java

DrawableTypeRequest(Class<ModelType> modelClass, ModelLoader<ModelType, InputStream> streamModelLoader,
            ModelLoader<ModelType, ParcelFileDescriptor> fileDescriptorModelLoader, Context context, Glide glide,
            RequestTracker requestTracker, Lifecycle lifecycle, RequestManager.OptionsApplier optionsApplier) {
    super(context, modelClass,
            buildProvider(glide, streamModelLoader, fileDescriptorModelLoader, GifBitmapWrapper.class,
                    GlideDrawable.class, null),
            glide, requestTracker, lifecycle);
    this.streamModelLoader = streamModelLoader;
    this.fileDescriptorModelLoader = fileDescriptorModelLoader;
    this.optionsApplier = optionsApplier;
}
```

