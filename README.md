# SIU-PrecisionAgri-DisasterResilience

## Overview
This repository showcases two advanced Shiny applications developed to support Southern Illinois University (SIU) Carbondale’s mission of fostering agricultural innovation and community resilience. As part of my work in the MELT-VET program, I leveraged **RStudio**, **AI**, and **data science** to create tools that address real-world challenges in precision agriculture and disaster preparedness. These projects demonstrate SIU’s potential to lead in sustainable farming practices and disaster recovery, supporting both the university community and the broader Southern Illinois region.

### Project 1: SIU AgriVision AR - Precision Agriculture Dataset
#### Objective
Develop a dataset to support an augmented reality (AR) app concept for precision agriculture at SIU University Farms, enabling farmers to visualize soil health, crop yield, and livestock data in real-time.

#### Dataset Description
- **File**: `siu_stadiamap_agri.csv`
- **Description**: A simulated dataset representing a 10-acre plot at SIU University Farms, with 50 grid points. Columns include `Point_ID`, `X_Coord`, `Y_Coord`, `Elevation`, `Nitrogen_ppm`, `Potassium_ppm`, `Soybean_Yield`, and `Livestock_Feeding`.
- **Download**: [siu_stadiamap_agri.csv](siu_stadiamap_agri.csv)

#### Insights
- Provides foundational data for precision agriculture, capturing soil health (nitrogen, potassium), soybean yield, and livestock feeding efficiency.
- Supports the development of an AR app ("SIU AgriVision AR") to make agricultural data actionable in the field.

---

### Project 2: SIU DroneAgri Dashboard - Real-Time Crop and Livestock Monitoring
#### Objective
Create an interactive Shiny dashboard to visualize drone-collected data (crop health, soil moisture, livestock movement) at SIU University Farms over the 2025 growing season, empowering farmers with real-time insights.

#### Dataset Description
- **File**: `siu_drone_data.csv`
- **Description**: A simulated dataset extending `siu_stadiamap_agri.csv` with a time dimension (May to September 2025). Columns include `Point_ID`, `X_Coord`, `Y_Coord`, `Elevation`, `Soybean_Yield`, `Livestock_Feeding`, `Month`, `NDVI`, `Soil_Moisture`, and `Livestock_Count`.
- **Download**: [siu_drone_data.csv](siu_drone_data.csv)

#### Code
```R
# Load libraries
library(shiny)
library(tidyverse)
library(ggplot2)
library(gganimate)

# Load the drone dataset
drone_data <- read_csv("siu_drone_data.csv")

# Define the UI
ui <- fluidPage(
  titlePanel("SIU DroneAgri Dashboard: Real-Time Crop and Livestock Monitoring"),
  sidebarLayout(
    sidebarPanel(
      selectInput("month", "Select Month:", choices = unique(drone_data$Month)),
      checkboxInput("animate", "Animate Over Time", value = FALSE),
      p("Use the dropdown to view drone-collected data for each month of the 2025 growing season. Check the box to animate the data over time.")
    ),
    mainPanel(
      plotOutput("drone_map", height = "600px")
    )
  )
)

# Define the server
server <- function(input, output) {
  output$drone_map <- renderPlot({
    if (!input$animate) {
      month_data <- drone_data %>% filter(Month == input$month)
      ggplot(month_data, aes(x = X_Coord, y = Y_Coord)) +
        geom_point(aes(color = NDVI, size = 5), alpha = 0.7) +
        scale_color_gradient(low = "yellow", high = "darkgreen", name = "NDVI (Crop Health)") +
        scale_size_identity() +
        geom_contour(aes(z = Soil_Moisture), color = "blue", size = 1, bins = 5) +
        geom_rect(aes(xmin = 400, xmax = 450, ymin = 0, ymax = 200), 
                  fill = NA, color = "blue", size = 1, linetype = "dashed") +
        geom_point(data = filter(month_data, !is.na(Livestock_Count)),
                   aes(size = Livestock_Count), color = "purple", shape = 21, fill = "white") +
        scale_size_continuous(range = c(3, 10), name = "Cows Detected") +
        coord_fixed(ratio = 1) +
        theme_minimal() +
        theme(
          panel.background = element_rect(fill = "#f5f5f5", color = NA),
          panel.grid = element_blank(),
          plot.title = element_text(size = 14, face = "bold", hjust = 0.5)
        ) +
        labs(
          title = paste("SIU University Farms: Drone Data for", input$month, "2025"),
          x = "X Coordinate (m)",
          y = "Y Coordinate (m)"
        )
    } else {
      p <- ggplot(drone_data, aes(x = X_Coord, y = Y_Coord)) +
        geom_point(aes(color = NDVI, size = 5), alpha = 0.7) +
        scale_color_gradient(low = "yellow", high = "darkgreen", name = "NDVI (Crop Health)") +
        scale_size_identity() +
        geom_contour(aes(z = Soil_Moisture), color = "blue", size = 1, bins = 5) +
        geom_rect(aes(xmin = 400, xmax = 450, ymin = 0, ymax = 200), 
                  fill = NA, color = "blue", size = 1, linetype = "dashed") +
        geom_point(data = filter(drone_data, !is.na(Livestock_Count)),
                   aes(size = Livestock_Count), color = "purple", shape = 21, fill = "white") +
        scale_size_continuous(range = c(3, 10), name = "Cows Detected") +
        coord_fixed(ratio = 1) +
        theme_minimal() +
        theme(
          panel.background = element_rect(fill = "#f5f5f5", color = NA),
          panel.grid = element_blank(),
          plot.title = element_text(size = 14, face = "bold", hjust = 0.5)
        ) +
        labs(
          title = "SIU University Farms: Drone Data for {closest_state} 2025",
          x = "X Coordinate (m)",
          y = "Y Coordinate (m)"
        ) +
        transition_states(Month, transition_length = 2, state_length = 1) +
        ease_aes("linear")
      animate(p, nframes = 50, fps = 10, width = 800, height = 600, renderer = gifski_renderer("drone_animation.gif"))
    }
  })
}

# Run the Shiny app
shinyApp(ui = ui, server = server)
```

#### Output
- **Dashboard**: An interactive Shiny app showing drone-collected data for each month.
- **Animation**: [Drone Animation](<img width="959" alt="Screenshot 2025-03-24 233630" src="https://github.com/user-attachments/assets/f396a484-fca5-4db9-bde1-5919e801df61" />
) showing changes in NDVI, soil moisture, and livestock movement over the growing season.
- **Insights**: Highlights areas with low crop health for intervention, identifies dry areas, and tracks livestock movement in the cattle pen.

---

### Project 3: SIU Disaster Resilience Predictor - AI-Powered Disaster Preparedness
#### Objective
Use AI to predict the impact of natural disasters (floods, tornadoes) on Southern Illinois communities, focusing on agricultural areas, infrastructure, and SIU students, and create an interactive Shiny dashboard to visualize the results.

#### Dataset Description
- **File**: `siu_disaster_resilience.csv`
- **Description**: Simulated data for 100 communities over 5 years (2025-2030) with columns: `Community_ID`, `Year`, `Location`, `Flood_Risk`, `Tornado_Risk`, `Flood_Occurrence`, `Tornado_Occurrence`, `Crop_Loss`, `Infrastructure_Damage`, `Student_Displacement`, `Recovery_Cost`, `Preparedness_Score`.
- **Download**: [siu_disaster_resilience.csv](siu_disaster_resilience.csv)

#### Code
```R
# Load libraries
library(shiny)
library(tidyverse)
library(ggplot2)
library(gganimate)
library(ranger)

# Load the dataset and model
disaster_data <- read_csv("siu_disaster_resilience.csv")
rf_model <- readRDS("disaster_rf_model.rds")

# Define the UI
ui <- fluidPage(
  titlePanel("SIU Disaster Resilience Predictor: Supporting Southern Illinois Communities"),
  sidebarLayout(
    sidebarPanel(
      selectInput("location", "Select Community:", choices = unique(disaster_data$Location)),
      sliderInput("year", "Select Year:", min = 2025, max = 2029, value = 2025, step = 1),
      checkboxInput("animate", "Animate Over Time", value = FALSE),
      h4("Predict Recovery Cost for a Custom Scenario"),
      sliderInput("flood_risk", "Flood Risk (0-100):", min = 0, max = 100, value = 50),
      sliderInput("tornado_risk", "Tornado Risk (0-100):", min = 0, max = 100, value = 50),
      sliderInput("preparedness", "Preparedness Score (0-100):", min = 0, max = 100, value = 50),
      actionButton("predict", "Predict Recovery Cost"),
      textOutput("prediction"),
      p("Explore disaster impacts and predict recovery costs for Southern Illinois communities.")
    ),
    mainPanel(
      plotOutput("disaster_plot", height = "600px"),
      plotOutput("impact_plot", height = "400px")
    )
  )
)

# Define the server
server <- function(input, output) {
  output$disaster_plot <- renderPlot({
    if (!input$animate) {
      plot_data <- disaster_data %>% 
        filter(Location == input$location, Year == input$year)
      ggplot(plot_data, aes(x = factor(Year))) +
        geom_bar(aes(y = Crop_Loss, fill = "Crop Loss"), stat = "identity", position = "dodge") +
        geom_bar(aes(y = Infrastructure_Damage, fill = "Infrastructure Damage"), stat = "identity", position = "dodge") +
        geom_bar(aes(y = Student_Displacement * 1000, fill = "Student Displacement Cost"), stat = "identity", position = "dodge") +
        scale_fill_manual(values = c("Crop Loss" = "darkgreen", "Infrastructure Damage" = "gray", "Student Displacement Cost" = "purple")) +
        labs(
          title = paste("Disaster Impacts in", input$location, "for", input$year),
          x = "Year",
          y = "Impact (Dollars)",
          fill = "Impact Type"
        ) +
        theme_minimal()
    } else {
      plot_data <- disaster_data %>% filter(Location == input$location)
      p <- ggplot(plot_data, aes(x = factor(Year))) +
        geom_bar(aes(y = Crop_Loss, fill = "Crop Loss"), stat = "identity", position = "dodge") +
        geom_bar(aes(y = Infrastructure_Damage, fill = "Infrastructure Damage"), stat = "identity", position = "dodge") +
        geom_bar(aes(y = Student_Displacement * 1000, fill = "Student Displacement Cost"), stat = "identity", position = "dodge") +
        scale_fill_manual(values = c("Crop Loss" = "darkgreen", "Infrastructure Damage" = "gray", "Student Displacement Cost" = "purple")) +
        labs(
          title = paste("Disaster Impacts in", input$location, "({closest_state})"),
          x = "Year",
          y = "Impact (Dollars)",
          fill = "Impact Type"
        ) +
        theme_minimal() +
        transition_states(Year, transition_length = 2, state_length = 1) +
        ease_aes("linear")
      animate(p, nframes = 50, fps = 10, width = 800, height = 600, renderer = gifski_renderer("disaster_animation.gif"))
    }
  })
  
  output$impact_plot <- renderPlot({
    plot_data <- disaster_data %>% filter(Location == input$location)
    ggplot(plot_data, aes(x = Preparedness_Score, y = Recovery_Cost, color = factor(Year))) +
      geom_point(size = 5, alpha = 0.7) +
      geom_line(aes(group = 1), color = "black", linetype = "dashed") +
      scale_color_brewer(palette = "Set1", name = "Year") +
      labs(
        title = paste("Preparedness vs. Recovery Cost in", input$location),
        x = "Preparedness Score (0-100)",
        y = "Recovery Cost (Dollars)"
      ) +
      theme_minimal()
  })
  
  observeEvent(input$predict, {
    custom_data <- data.frame(
      Location = input$location,
      Flood_Risk = input$flood_risk,
      Tornado_Risk = input$tornado_risk,
      Preparedness_Score = input$preparedness
    )
    prediction <- predict(rf_model, data = custom_data)$predictions
    output$prediction <- renderText({
      paste("Predicted Recovery Cost: $", round(prediction, 2))
    })
  })
}

# Run the Shiny app
shinyApp(ui = ui, server = server)
```

#### Output
- **Dashboard**: An interactive Shiny app showing disaster impacts, preparedness trends, and AI-predicted recovery costs.
- **Animation**: [Disaster Animation](<img width="959" alt="Screenshot 2025-03-24 234500" src="https://github.com/user-attachments/assets/8d2a6f43-5ce7-4fd7-86d2-20197aa42802" />
) showing changes in disaster impacts over time.
- **Insights**: Highlights communities at high risk, estimates recovery costs, and emphasizes the importance of preparedness in reducing impacts.

---

## Why This Matters
These projects position SIU-Carbondale as a leader in precision agriculture and disaster resilience, addressing critical challenges in Southern Illinois. The **SIU DroneAgri Dashboard** empowers local farmers with real-time data to optimize crop health and livestock management, supporting sustainable agriculture in a region where farming is a cornerstone of the economy. The **SIU Disaster Resilience Predictor** leverages AI to predict the impacts of floods and tornadoes, enabling SIU to support rural communities, protect agricultural areas, and ensure student safety during natural disasters. Together, these tools demonstrate SIU’s commitment to innovation, community service, and resilience, while showcasing my leadership and technical skills as a MELT-VET participant.

## Conclusion
The **SIU-PrecisionAgri-DisasterResilience** repository highlights the transformative potential of AI and data science in agriculture and disaster preparedness. By combining advanced datasets with interactive Shiny applications, these projects provide actionable insights for SIU University Farms and the broader Southern Illinois community. I am excited to share this work with Associate Professor Justin McDaniel, demonstrating how SIU can lead in sustainable farming and disaster resilience while fostering regional growth and safety.

---

Built with RStudio, leveraging AI for predictive modeling and visualization. 📊

#DataScience #MachineLearning #AI #SIUCarbondale #AgriculturalInnovation #PrecisionAgriculture #DisasterResilience #RStudio
```

---

### How to Share with Dr. McDaniel

1. **Create the Repository**:
   - Create a new GitHub repository named `SIU-PrecisionAgri-DisasterResilience`.
   - Upload the datasets (`siu_stadiamap_agri.csv`, `siu_drone_data.csv`, `siu_disaster_resilience.csv`), the model file (`disaster_rf_model.rds`), and the animation files (`drone_animation.gif`, `disaster_animation.gif`).
   - Add the README content above to `README.md`.

2. **Email to Dr. McDaniel**:
   Here’s a professional email draft to share the repository:

   **Subject**: MELT-VET Project: Precision Agriculture and Disaster Resilience Tools for SIU-Carbondale

3. **LinkedIn Post (Optional)**:
   You can also share this on LinkedIn to highlight your work to a broader audience:

   🌟 **Advancing Precision Agriculture and Disaster Resilience at SIU-Carbondale!** 🌟

   I’m excited to share my latest MELT-VET project: two AI-powered Shiny apps to support SIU-Carbondale’s mission in Southern Illinois. 🚀

   ✅ **SIU DroneAgri Dashboard**: Real-time crop and livestock monitoring for University Farms.  
   ✅ **SIU Disaster Resilience Predictor**: AI-driven predictions for flood and tornado impacts, supporting community preparedness.

   Check out the repository: [Insert GitHub Link]  
   Built with RStudio, leveraging AI for predictive modeling and visualization. 📊

   #DataScience #MachineLearning #AI #SIUCarbondale #AgriculturalInnovation #PrecisionAgriculture #DisasterResilience

---

### Why This README is Compelling and Professional
- **Clear Structure**: The README is organized into sections (Overview, Projects, Why This Matters, Conclusion), making it easy for Dr. McDaniel to navigate.
- **Professional Tone**: The language is formal yet enthusiastic, emphasizing the impact of your work and your role in MELT-VET.
- **Focus on Impact**: The "Why This Matters" section ties the projects to SIU’s mission and the needs of Southern Illinois, showing relevance and vision.
- **Technical Details**: The inclusion of datasets, code, and outputs demonstrates your technical expertise while providing transparency.
- **Visual Appeal**: Links to animations and clear formatting make the README engaging and professional.
