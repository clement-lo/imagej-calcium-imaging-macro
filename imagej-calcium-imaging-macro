// ImageJ Macro: Process Specific .zip File with User Interaction
macro "Process Zip File with ROIs" {
    // Initialize an array to keep track of loaded ROI .zip files
 loadedRoiPaths = newArray();
    processAnotherFile = true;
    roisEdited = false;
    roisLoaded = false;
    roiZipPath = "";
    while (processAnotherFile) {
        // Close all open images and reset necessary components
 		run("Close All");
        roiManager("Reset");
        run("Clear Results");

        // 1. Prompt for Images
        zipFilePath = File.openDialog("Select a .zip file for analysis");
        open(zipFilePath);
        if (zipFilePath == 0) {
            showMessage("Exit", "No file selected. Exiting macro.");
            return; // Exit the macro if no file is selected
        }
      
     // 2. Open the .zip image
waitForUser("Please wait for the images to load.");
imageList = getImageTitles();
if (imageList.length == 0) {
    showMessage("Error", "No images were loaded from the zip file.");
    return; // Exit the macro if no images were loaded to prevent further errors
}
showMessage("Notice", "The macro will now move to the last slice in the stack to ensure all images are properly loaded and ready for processing.");

setSlice(nSlices); // Move to the last slice in the stack
if (waitForUserConfirmation("Confirm Slice", "You are currently viewing the last slice in the stack. Is this the correct position to start your analysis?") == 0) {
    showMessage("Notice", "Adjust the slice position as needed and then proceed.");
    return; // Exit the macro if the user indicates the position is not correct
}
    
// 3. Prompt for manual Brightness/Contrast Adjustments
for (i = 0; i < imageList.length; i++) {
    showMessage("Adjustment Instructions", "You are adjusting image: " + imageList[i] + "\nPlease adjust Brightness & Contrast for better visibility.");
    showImage(imageList[i]);
    run("Brightness/Contrast...");
    waitForUser("After adjustments, click OK to continue.");
}
// 4. Decision Dialog for ROIs
decision = showDialog("ROI Management", "Would you like to start by drawing new ROIs?", "Draw new ROIs", false);
if (decision == 1) {
    // User chose to draw new ROIs. Proceed with instructions to draw new ROIs.
    printInstructionsForROIs();
    // Implicitly, roisLoaded is false here. No need to set roisLoaded = false again.
} else {
    roiZipPath = File.openDialog("Select a .zip file containing ROIs if available.");
    // Ensure roiZipPath is correctly checked for null (cancellation or no selection)
		if (roiZipPath != "") {
        if (!arrayContains(loadedRoiPaths, roiZipPath)) {
            roiManager("Open", roiZipPath);
            loadedRoiPaths = Array.concat(loadedRoiPaths, roiZipPath);
            roisLoaded = true; // Correctly indicate that ROIs have been loaded
        } else {
            showMessage("Notice", "This ROI file has already been loaded.");
        }
    } else {
        // If no file was selected (user canceled the dialog), proceed as if deciding to draw new ROIs
        printInstructionsForROIs();
    }
}
// Continue with drawing or reviewing ROIs as necessary
waitForUser("Draw the first ROI (Press 't' to add to ROI Manager) or adjust loaded ROIs.");
roiManager("show all with labels");
waitForUser("When all ROIs are added or reviewed, press 'OK'.");

// Step 5: Save ROIs for the .zip file (Allows User Input for File Name only if needed)
// Extract the base file name without extension
baseFileName = getTitle();
dotIndex = baseFileName.lastIndexOf(".");
if (dotIndex > 0) {
    // Remove the extension from the baseFileName
    baseFileName = baseFileName.substring(0, dotIndex);
}
// Default saveName with "_ROIs.zip" appended
saveName = baseFileName + "_ROIs.zip";

// Ensure directory retrieval is successful
currentDirectory = getDirectory("current");
// Corrected null and empty string check
if (currentDirectory == "" || lengthOf(currentDirectory) == 0) {
    showMessage("Error", "Failed to retrieve the current directory. Please ensure you have write access.");
    return; // Exit if the directory cannot be retrieved
}

// Construct savePath using the updated saveName
savePath = currentDirectory + saveName;

// Perform the save operation if there are ROIs to save
if (roiManager("Count") > 0) {
    roiManager("Deselect"); // Deselect any selected ROIs
    roiManager("Save", savePath); // Save the ROIs to the specified path
    showMessage("Success", "ROIs saved successfully at: " + savePath);
} else {
    showMessage("Notice", "No ROIs to save.");
}
            // 6. Measurement Selection Based on ROI Count
           if (roiManager("Count") > 0) {
    roiManager("multi-measure one");
} else {
    showMessage("Notice", "No ROIs available for measurement.");
}
// 7. Ask if the user wants to save measurements before processing another .zip file
saveResults = showDialog("Save Results", "Do you wish to save the multi-measure results as a new .csv file?", "Yes", true);
if (saveResults) {
    saveMeasurements(); // This function is called only if the user chooses to save the results
}
waitForUser("Export the multi-measure results to Excel. Press 'OK' after reviewing the data in Excel to proceed with time frame analysis in FIJI.");
processROIsInBatches(roiZipPath);
   roiBatchSize = getNumber("Enter the number of ROIs to review per batch:", 10);
        processAnotherBatch = true;
        while (processAnotherBatch) {
            processROIsInBatches(roiZipPath, roiBatchSize);
            processAnotherBatch = showMessage("Completed", "Reviewing ROIs in batches of " + roiBatchSize + ".", "Process next batch?");
            if (!processAnotherBatch) break; // Proceed with step 8 if not processing another batch
        }
        
// 8. Ask for additional .zip to repeat steps 1-7
showMessage("Completed", "Processing and reviewing completed.");
        processAnotherFile = showDialog("Process Another File", "Do you want to process and review another .zip file?", "Yes", false);
        if (!processAnotherFile) {
            break; // Exit the loop if the user does not want to process another file
    zipFilePath = File.openDialog("Select a .zip file for analysis");
    }
    showMessage("Macro Finished", "All selected files have been processed.");
}

function saveMeasurements() {
    // Only proceed if there are results to save
    if (nResults > 0) {
        // Ask for the directory and filename for the CSV output
        path = getDirectory("Choose a directory to save the measurements CSV file");
        // Extract the filename from the ROI .zip path and change its extension to .csv
        defaultFileName = roiZipPath.substring(roiZipPath.lastIndexOf("/") + 1);
        // Check if there is an extension to replace, if not, append .csv
        if (defaultFileName.lastIndexOf(".") != -1) {
            defaultFileName = defaultFileName.substring(0, defaultFileName.lastIndexOf(".")) + ".csv";
        } else {
            defaultFileName += ".csv";
        }
        // Prompt user for filename, suggesting the modified defaultFileName
        filename = getString("Enter a filename for the CSV", defaultFileName);
        // Construct the full path for the results file
        fullPath = path + filename;
        // Save the measurements
        saveAs("Results", fullPath);
        // Notify the user of the save location
        showMessage("Data Saved", "The measurements were saved to " + fullPath);
    } else {
        // Notify the user if there are no measurements to save
        showMessage("No Data", "There were no measurements to save.");
    }
}

function getImageTitles() {
    count = nImages;
    titles = newArray(count);
    for (i = 0; i < count; i++) {
        selectImage(i + 1);
        titles[i] = getTitle();
    }
    return titles;
}
function showImage(title) {
    selectWindow(title);
    run("Set Scale...", "distance=0 known=0 pixel=1 unit=pixel");
    resetMinAndMax();
    updateDisplay();
}
function showDialog(title, message, checkboxLabel, checkboxDefault) {
    Dialog.create(title);
    Dialog.addMessage(message);
    Dialog.addCheckbox(checkboxLabel, checkboxDefault);
    Dialog.show();
    return Dialog.getCheckbox();
}

function waitForUserConfirmation(title, message) {
    Dialog.create(title);
    Dialog.addMessage(message);
    Dialog.addCheckbox("Yes, proceed with analysis.", true);
    Dialog.show();
    if (Dialog.getCheckbox()) {
        return 1;
    } else {
        return 0;
    }
}

function printInstructionsForROIs() {
    print("ROI Management Instructions:");
    print("1. Select ROIs on key features (e.g., Oval/Ellipse for DRG Neurons).");
    print("2. Avoid empty areas to ensure measurements are meaningful.");
    print("3. Prevent ROI overlap to maintain accurate analysis.");
    print("4. Use 't' to add selected ROIs to the ROI Manager.");
    print("5. Adjust brightness/contrast for visibility if necessary.");
    if (roisLoaded) {
        print("6. Review loaded ROIs for accuracy and make adjustments as needed.");
    } else {
        print("6. Draw new ROIs or adjust existing ones using drawing tools.");
    }
    print("7. Manage ROIs (add, rename, delete) via ROI Manager for organization.");
    print("Complete your ROI selection or adjustments, then click 'OK' to proceed.");
}

// Function to check if an array contains a specific item
function arrayContains(arr, item) {
    for (i = 0; i < lengthOf(arr); i++) {
        if (arr[i] == item) {
            return true;
        }
    }
    return false;
}

function processROIsInBatches(zipPath, batchSize) {
    roiManager("Reset");
    roiManager("Open", zipPath);
    totalROIs = roiManager("count");
    batches = Math.ceil(totalROIs / batchSize);
    
    for (var batch = 0; batch < batches; batch++) {
        // Process each batch of ROIs
        showMessage("Batch Processing", "Processing batch " + (batch + 1) + " of " + batches);
        reviewROIsInBatch(batch, batchSize, totalROIs, zipPath);
        
        if (!showDialog("Continue with next batch?", "Would you like to continue reviewing ROIs in the next batch?")) break;
    }
}

function reviewROIsInBatch(batch, batchSize, totalROIs, zipPath) {
    var startROI = batch * batchSize;
    var endROI = Math.min(startROI + batchSize, totalROIs);
    var currentROIs = getROIsInRange(startROI, endROI);
 handlePlaybackReviews(currentROIs);
}

function getROIsInRange(startROI, endROI) {
    // Returns an array of ROI names or indexes within the specified range
    // Placeholder for actual implementation
    return newArray(); // Replace with actual logic to fetch ROIs
}

// Function to start and instruct on playback
function startPlayback(startFrame, endFrame, title) {
    startFrame = parseInt(startFrame); // Ensure startFrame is an integer
    endFrame = parseInt(endFrame); // Ensure endFrame is an integer

    // Check if startFrame and endFrame are numbers and within the stack range
    if (!isNumeric(startFrame) || !isNumeric(endFrame)) {
        showMessage("Playback Error", "Start frame or end frame is not a number.");
        return;
    }
    if (startFrame < 1 || startFrame >= endFrame) {
        showMessage("Playback Error", "Start frame is less than 1 or not less than end frame.");
        return;
    }
    if (endFrame > nSlices) {
        showMessage("Playback Error", "End frame is greater than the number of images in the stack.");
        return;
    }

function handlePlaybackReviews(currentROIs) {
	// Prompt for KCL Time Frame
kclStartFrame = getNumber("Enter the start frame for high KCl extracellular solution:", 1);
kclEndFrame = getNumber("Enter the total number of frames (end of time series):", nSlices);
startPlayback(kclStartFrame, kclEndFrame, "Review KCL Time Frame");

// Prompt for Condition/Intervention Time Frame
conditionStartFrame = getNumber("Enter the start frame for condition/intervention:", 1);
conditionEndFrame = getNumber("Enter the frame before high KCl solution starts:", kclStartFrame - 1);
startPlayback(conditionStartFrame, conditionEndFrame, "Review Condition/Intervention Time Frame");

// Prompt to check baseline response
checkBaseline = showDialog("Check Baseline", "Do you want to review the baseline response?", "Yes", false);
if (checkBaseline) {
    baselineStartFrame = 1; // Assuming baseline starts from the first frame
    startPlayback(baselineStartFrame, conditionStartFrame - 1, "Review Baseline Response");
}

}
    // Proceed with playback if all checks pass
    showMessage(title, "Playback will start from frame " + startFrame + " to frame " + endFrame + ".");
    setSlice(startFrame);
    run("Animation Options...", "speed=50 first=" + startFrame + " last=" + endFrame + " loop=false");
    run("Start Animation [\\]");
}

// Function to show a simple message dialog
function showMessage(title, message) {
    Dialog.create(title);
    Dialog.addMessage(message);
    Dialog.show();
}
// Function to check if a value is numeric
function isNumeric(n) {
    return !isNaN(parseFloat(n)) && isFinite(n);
}