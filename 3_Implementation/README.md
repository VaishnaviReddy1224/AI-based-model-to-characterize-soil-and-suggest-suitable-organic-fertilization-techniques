classdef Capstone < matlab.apps.AppBase

    % Properties that correspond to app components
    properties (Access = public)
        UIFigure                        matlab.ui.Figure
        Image2                          matlab.ui.control.Image
        Image                           matlab.ui.control.Image
        CropandFertilizationRecommendationLabel  matlab.ui.control.Label
        Button                          matlab.ui.control.Button
        OrganicFertilizationRecommendationTextArea  matlab.ui.control.TextArea
        OrganicFertilizationRecommendationTextAreaLabel  matlab.ui.control.Label
        CropRecommendationTextArea      matlab.ui.control.TextArea
        CropRecommendationTextAreaLabel  matlab.ui.control.Label
        PhValueEditField                matlab.ui.control.NumericEditField
        PhValueEditFieldLabel           matlab.ui.control.Label
        PotassiumValueEditField         matlab.ui.control.NumericEditField
        PotassiumValueEditFieldLabel    matlab.ui.control.Label
        PhosphorousValueEditField       matlab.ui.control.NumericEditField
        PhosphorousValueEditFieldLabel  matlab.ui.control.Label
        NitrogenValueEditField          matlab.ui.control.NumericEditField
        NitrogenValueEditFieldLabel     matlab.ui.control.Label
    end

    % Callbacks that handle component events
    methods (Access = private)

        % Button pushed function: Button
        function ButtonPushed(app, event)
            data = readtable('Crop_recommendation - Copy.csv');

            % Step 2: Extract user input values
            N = app.NitrogenValueEditField.Value;
            P = app.PhosphorousValueEditField.Value;
            K = app.PotassiumValueEditField.Value;
            Ph = app.PhValueEditField.Value;

            % Step 3: Calculate Euclidean distance to find the closest match
            distances = sqrt((data.N - N).^2 + (data.P - P).^2 + (data.K - K).^2 + (data.ph - Ph).^2);
            [~, idx] = min(distances);
    
            % Step 4: Get the recommended crop
            recommendedCrop = data.label(idx);
    
            % Step 5: Display the recommended crop in the Crop Text Area
            app.CropRecommendationTextArea.Value = sprintf('Recommended Crop: %s', recommendedCrop{1});
            
            % Organic Fertilization Recommendation Logic
            low_threshold = 10;
            high_threshold = 150;

            % Nitrogen Level
            if N < low_threshold
                N_recommendation = '- Low N: Use compost, manure, blood meal.';
            elseif N > high_threshold
                N_recommendation = '- High N: Avoid excess fertilization, plant legumes.';
            else
                N_recommendation = '- N level is normal, maintain balance.';
            end

            % Phosphorus Level
            if P < low_threshold
                P_recommendation = '- Low P: Add bone meal, rock phosphate.';
            elseif P > high_threshold
                P_recommendation = '- High P: Reduce P-fertilizers, add organic matter.';
            else
                P_recommendation = '- P level is normal, maintain balance.';
            end

            % Potassium Level
            if K < low_threshold
                K_recommendation = '- Low K: Apply wood ash, compost.';
            elseif K > high_threshold
                K_recommendation = '- High K: Avoid excess K-fertilizers.';
            else
                K_recommendation = '- K level is normal, maintain balance.';
            end
            
            if Ph < 6.0
                Ph_recommendation = '- Low pH: Apply lime, wood ash, or bone meal to increase pH.';
            elseif Ph > 7.5
                Ph_recommendation = '- High pH: Add sulfur, peat moss, or compost to lower pH.';
            else
                Ph_recommendation = '- pH is balanced, no adjustment needed.';
            end

            % Combine all recommendations
            final_recommendation = sprintf('%s\n%s\n%s\n%s', N_recommendation, P_recommendation, K_recommendation,Ph_recommendation);

            % Display the fertilizer recommendation in the Fert Text Area
            app.OrganicFertilizationRecommendationTextArea.Value = final_recommendation;
        end
    end

    % Component initialization
    methods (Access = private)

        % Create UIFigure and components
        function createComponents(app)

            % Create UIFigure and hide until all components are created
            app.UIFigure = uifigure('Visible', 'off');
            app.UIFigure.Color = [1 1 1];
            app.UIFigure.Position = [100 100 640 480];
            app.UIFigure.Name = 'MATLAB App';
            app.UIFigure.Icon = 'image.jpg';

            % Create NitrogenValueEditFieldLabel
            app.NitrogenValueEditFieldLabel = uilabel(app.UIFigure);
            app.NitrogenValueEditFieldLabel.HorizontalAlignment = 'right';
            app.NitrogenValueEditFieldLabel.Position = [86 330 80 22];
            app.NitrogenValueEditFieldLabel.Text = 'NitrogenValue';

            % Create NitrogenValueEditField
            app.NitrogenValueEditField = uieditfield(app.UIFigure, 'numeric');
            app.NitrogenValueEditField.Position = [181 330 100 22];

            % Create PhosphorousValueEditFieldLabel
            app.PhosphorousValueEditFieldLabel = uilabel(app.UIFigure);
            app.PhosphorousValueEditFieldLabel.HorizontalAlignment = 'right';
            app.PhosphorousValueEditFieldLabel.Position = [314 330 106 22];
            app.PhosphorousValueEditFieldLabel.Text = 'PhosphorousValue';

            % Create PhosphorousValueEditField
            app.PhosphorousValueEditField = uieditfield(app.UIFigure, 'numeric');
            app.PhosphorousValueEditField.Position = [435 330 100 22];

            % Create PotassiumValueEditFieldLabel
            app.PotassiumValueEditFieldLabel = uilabel(app.UIFigure);
            app.PotassiumValueEditFieldLabel.HorizontalAlignment = 'right';
            app.PotassiumValueEditFieldLabel.Position = [78 238 91 22];
            app.PotassiumValueEditFieldLabel.Text = 'PotassiumValue';

            % Create PotassiumValueEditField
            app.PotassiumValueEditField = uieditfield(app.UIFigure, 'numeric');
            app.PotassiumValueEditField.Position = [184 238 100 22];

            % Create PhValueEditFieldLabel
            app.PhValueEditFieldLabel = uilabel(app.UIFigure);
            app.PhValueEditFieldLabel.HorizontalAlignment = 'right';
            app.PhValueEditFieldLabel.Position = [371 238 49 22];
            app.PhValueEditFieldLabel.Text = 'PhValue';

            % Create PhValueEditField
            app.PhValueEditField = uieditfield(app.UIFigure, 'numeric');
            app.PhValueEditField.Position = [435 238 100 22];

            % Create CropRecommendationTextAreaLabel
            app.CropRecommendationTextAreaLabel = uilabel(app.UIFigure);
            app.CropRecommendationTextAreaLabel.HorizontalAlignment = 'right';
            app.CropRecommendationTextAreaLabel.Position = [17 113 99 30];
            app.CropRecommendationTextAreaLabel.Text = {'Crop '; 'Recommendation'};

            % Create CropRecommendationTextArea
            app.CropRecommendationTextArea = uitextarea(app.UIFigure);
            app.CropRecommendationTextArea.Position = [131 85 150 60];

            % Create OrganicFertilizationRecommendationTextAreaLabel
            app.OrganicFertilizationRecommendationTextAreaLabel = uilabel(app.UIFigure);
            app.OrganicFertilizationRecommendationTextAreaLabel.HorizontalAlignment = 'right';
            app.OrganicFertilizationRecommendationTextAreaLabel.Position = [323 113 115 30];
            app.OrganicFertilizationRecommendationTextAreaLabel.Text = {'Organic Fertilization '; 'Recommendation'; ''};

            % Create OrganicFertilizationRecommendationTextArea
            app.OrganicFertilizationRecommendationTextArea = uitextarea(app.UIFigure);
            app.OrganicFertilizationRecommendationTextArea.Position = [453 85 150 60];

            % Create Button
            app.Button = uibutton(app.UIFigure, 'push');
            app.Button.ButtonPushedFcn = createCallbackFcn(app, @ButtonPushed, true);
            app.Button.Position = [271 184 100 22];

            % Create CropandFertilizationRecommendationLabel
            app.CropandFertilizationRecommendationLabel = uilabel(app.UIFigure);
            app.CropandFertilizationRecommendationLabel.FontSize = 20;
            app.CropandFertilizationRecommendationLabel.FontWeight = 'bold';
            app.CropandFertilizationRecommendationLabel.FontColor = [0.4667 0.6745 0.1882];
            app.CropandFertilizationRecommendationLabel.Position = [126 393 390 60];
            app.CropandFertilizationRecommendationLabel.Text = 'Crop and Fertilization Recommendation';

            % Create Image
            app.Image = uiimage(app.UIFigure);
            app.Image.Position = [503 386 100 82];
            app.Image.ImageSource = 'image.jpg';

            % Create Image2
            app.Image2 = uiimage(app.UIFigure);
            app.Image2.Position = [38 388 89 80];
            app.Image2.ImageSource = 'image.jpg';

            % Show the figure after all components are created
            app.UIFigure.Visible = 'on';
        end
    end

    % App creation and deletion
    methods (Access = public)

        % Construct app
        function app = Capstone

            % Create UIFigure and components
            createComponents(app)

            % Register the app with App Designer
            registerApp(app, app.UIFigure)

            if nargout == 0
                clear app
            end
        end

        % Code that executes before app deletion
        function delete(app)

            % Delete UIFigure when app is deleted
            delete(app.UIFigure)
        end
    end
end
