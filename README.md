# json_to_voc
An example Python code that converts COCO JSON image labeling to Pascal VOC XML format.

This modified code iterates through all images in the COCO JSON file, and generates one Pascal VOC XML file for each image in the specified output folder. Note that the XML filename is derived from the corresponding image filename in the COCO JSON file.

Details:
First, the function coco_to_voc takes in two arguments: coco_json_path and output_folder. coco_json_path is the path to the COCO JSON file that you want to convert, and output_folder is the path to the folder where you want to save the Pascal VOC XML files:

def coco_to_voc(coco_json_path, output_folder):

Next, we open the COCO JSON file and load its contents into a Python dictionary using the json.load() function.

    with open(coco_json_path, 'r') as f:
        coco_data = json.load(f)
Then, we iterate through all images in the COCO JSON file using a for loop that iterates through the images key of the coco_data dictionary.

    for image in coco_data['images']:
    
Inside the loop, we create a new XML document root using the Element() function from the xml.etree.cElementTree module.

        root = ET.Element('annotation')

We then extract the image filename and size from the image dictionary, and add them to the XML document root as child elements using the SubElement() function.

        filename = image['file_name']
        size = image['height'], image['width'], image['depth']

        ET.SubElement(root, 'filename').text = filename
        ET.SubElement(root, 'size').text = f"{size[0]},{size[1]},{size[2]}"

Next, we iterate through all annotations in the COCO JSON file using another for loop that iterates through the annotations key of the coco_data dictionary. We only process annotations that belong to the current image by checking if the image_id key of the annotation dictionary matches the id key of the current image dictionary.

        for ann in coco_data['annotations']:
            if ann['image_id'] == image['id']:


Inside the loop, we create a new object element in the XML document root using the SubElement() function, and add the object name, pose, truncated, difficult, and bounding box information as child elements of the object element.

                obj = ET.SubElement(root, 'object')

                cat_id = ann['category_id']
                cat_name = coco_data['categories'][cat_id]['name']

                ET.SubElement(obj, 'name').text = cat_name
                ET.SubElement(obj, 'pose').text = 'Unspecified'
                ET.SubElement(obj, 'truncated').text = '0'
                ET.SubElement(obj, 'difficult').text = '0'

                bbox = ann['bbox']
                x_min, y_min, w, h = bbox[0], bbox[1], bbox[2], bbox[3]

                ET.SubElement(obj, 'bndbox').text = f"{int(x_min)},{int(y_min)},{int(x_min+w)},{int(y_min+h)}"

Finally, we save the XML file to the specified output folder with a filename that is derived from the corresponding image filename in the COCO JSON file.

        xml_filename = os.path.splitext(filename)[0] + '.xml'
        xml_filepath = os.path.join(output_folder, xml_filename)

        tree = ET.ElementTree(root)
        tree.write(xml_filepath)

        print(f"Conversion for image {filename} is completed successfully.")

Note that we import the os module at the beginning of the code to handle file paths and the xml.etree.cElementTree module to create and manipulate XML documents.

The code converts each image in the COCO JSON file to a separate Pascal VOC XML file, with the same filename as the original image file but with the .xml extension. The XML file contains information about the image filename, size, and all objects and their bounding boxes in the image.

Overall, this code demonstrates how to convert image labeling information in the COCO JSON format to the Pascal VOC XML format, which may be useful for different computer vision tasks that require data in a specific format.
