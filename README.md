# Mapaweb
library(leaflet)
library(leafpop)
library(dplyr)
#Librerías para botones
library(htmltools)
library(htmlwidgets)


# Definir lugar de trabajo

setwd("E:/W")

# Leer el archivo

Ct_A<-read.csv2("Centros de Acogida.csv")
Ct_A$Lon<-as.numeric(Ct_A$Longitud)
Ct_A$LAT<-as.numeric(Ct_A$Latitud)
Ct_A<-select(Ct_A,-c(Latitud,Longitud))
str(Ct_A)

# Añadiendo sistema de coordenadas
library(sf)
Ct_A<-st_as_sf(Ct_A,coords = c("Lon","LAT"), crs=4326)

#Funcion para diferenciar colores
cr<- colorFactor(c("blue", "Green"), domain = c(Ct_A$Tipo))
#Lectura

Ct_A %>% leaflet() %>% setView(lng = -77.0311,lat = -12.0453,zoom = 10 ) %>%
  #  adicionar capas
  
  addTiles(group = "OpenStreetMap",attribution = 'Mapa de Centros de Acogida' ) %>% 
  addProviderTiles(providers$Esri.WorldImagery, group = "Esri") %>%   
  addProviderTiles(providers$Stamen.TonerLite, group = "Toner Lite") %>%
  addProviderTiles(providers$CartoDB.DarkMatter, group = "CartoDB Dark") %>%
  
  
  #  adicionar marcadores

  
  addCircleMarkers(data= Ct_A , color = ~cr(Tipo) ,
                   popup = popupTable(Centros.de.Acogida),stroke = FALSE, fillOpacity = 0.5) %>%
  
  #adicionando control de capas
  
  addLayersControl(
    baseGroups = c("OpenStreetMap", "Esri", "Toner Lite","CartoDB Dark"),
    options = layersControlOptions(collapsed = TRUE)) %>%
  
  #Agregar leyenda

  
  addLegend(data = Ct_A,group ="Tipo" ,position = "bottomleft",pal = cr,values = ~Tipo,title = "Tipo de Refugio" ) %>%

  #Agregar un minimapa
  
  addProviderTiles(providers$Esri.WorldStreetMap,) %>%
  addMiniMap( tiles = providers$Esri.WorldStreetMap,
              toggleDisplay = TRUE, width = 150,
              height = 150,zoomLevelOffset =-3) %>%
  
  # Agregar botones
  
  addEasyButton(easyButton(
    icon="fa-globe", title="Zoom to Level 1",
    onClick=JS("function(btn, map){ map.setZoom(9); }")))
