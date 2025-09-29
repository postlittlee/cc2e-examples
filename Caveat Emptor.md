```java
if (deletePage(page) == E_OK) {
  if (registry.deleteReference(page.name) == E_OK) {
    if (configKeys.deleteKey(page.name.makeKey()) == E_OK){
      logger.log("page deleted");
      return E_OK;
    } else {
      logger.log("configKey not deleted");
      return E_ERROR;
    }
  } else {
    logger.log("deleteReference from registry failed");
    return E_ERROR;
  }
} else {
  logger.log("delete failed");
  return E_ERROR;
}
```

```java
if (deletePage(page) != E_OK) {
  logger.log("delete failed");
  return E_ERROR;
}

if (registry.deleteReference(page.name) != E_OK) {
  logger.log("deleteReference from registry failed");
  return E_ERROR;
}

if (configKeys.deleteKey(page.name.makeKey()) != E_OK){
  logger.log("configKey not deleted");
  return E_ERROR;
}

logger.log("page deleted");
return E_OK;
```

```java
try {
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
}
catch (Exception e) {
  logger.log(e.getMessage());
}
```
