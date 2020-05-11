# StLCrimeandParks
#Python code to find crime density around parks in St. Louis, MO
#Samuel Caputo

import arcpy
from arcpy import env
env.workspace = 
#Specify Parameters as needed
parks = arcpy.SetParameterAsText(0)
if parks == '#' or not parks:
    parks = "St. Louis City Parks\\parks" 
ViolCrimeRate = arcpy.SetParameterAsText(1)
if ViolCrimeRate == '#' or not ViolCrimeRate:
    ViolCrimeRate = "ViolCrimeRate" 
myworkspace = arcpy.SetParameterAstext(2)
if myworkspace == '#' or not myworkspace:
    myworkspace == "myworkspace"
   
#Variables needed:
parks = "parks"
c0_FeatureToPoint2 = "C:\\Users\\samcaputo28\\Documents\\ArcGIS\\Default.gdb\\c0_FeatureToPoint2"
ViolCrimeRate__2_ = ViolCrimeRate
c0_FeatureToPoint2_Buffer = "C:\\Users\\samcaputo28\\Documents\\ArcGIS\\Default.gdb\\c0_FeatureToPoint2_Buffer"
KernelD_c0_F2 = "C:\\Users\\samcaputo28\\Documents\\ArcGIS\\Default.gdb\\KernelD_c0_F2"
KernelD_c0_F2_Clip = "C:\\Users\\samcaputo28\\Documents\\ArcGIS\\Default.gdb\\KernelD_c0_F2_Clip"
KernelD_c0_F2_CopyRaster = "C:\\Users\\samcaputo28\\Documents\\ArcGIS\\Default.gdb\\KernelD_c0_F2_CopyRaster"

#Convert park polygons to point data
arcpy.FeatureToPoint_management(parks, c0_FeatureToPoint2, "CENTROID")

#Use park points to create 1 mile buffer around each park
arcpy.Buffer_analysis(c0_FeatureToPoint2, c0_FeatureToPoint2_Buffer, "1 Miles", "FULL", "ROUND", "NONE", "", "PLANAR")

#Select all violent crime rates around parks 
arcpy.SelectLayerByLocation_management(ViolCrimeRate, "WITHIN", c0_FeatureToPoint2_Buffer, "", "NEW_SELECTION", "NOT_INVERT")

#Determine kernel density of violent crime around parks
arcpy.gp.KernelDensity_sa(c0_FeatureToPoint2, "NONE", KernelD_c0_F2, "59.524", "", "SQUARE_KILOMETERS", "DENSITIES", "PLANAR")

#Clip new raster map to original violent crime layer
arcpy.Clip_management(KernelD_c0_F2, "-10054431.9424755 4654995.27785603 -10038240.8092145 4689399.79274578", KernelD_c0_F2_Clip, ViolCrimeRate, "-3.402823e+38", "NONE", "NO_MAINTAIN_EXTENT")

#Copy raster to form single dataset
arcpy.CopyRaster_management(KernelD_c0_F2, KernelD_c0_F2_CopyRaster, "", "", "-3.402823e+38", "NONE", "NONE", "", "NONE", "NONE", "", "NONE")

