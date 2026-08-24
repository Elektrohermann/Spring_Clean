Ignore this thats just smth i want to try and personalize for my use

Here’s a complete, safe, and efficient C# program that searches your entire PC for files of a specific type (extension), while skipping system and restricted folders to avoid access errors.
It uses Directory.EnumerateFiles with recursion and robust error handling.

Csharpusing System;
using System.Collections.Generic;
using System.IO;

class Program
{
    static void Main()
    {
        Console.Write("Enter file extension to search (e.g., .txt): ");
        string extension = Console.ReadLine()?.Trim();

        // Validate extension format
        if (string.IsNullOrWhiteSpace(extension) || !extension.StartsWith("."))
        {
            Console.WriteLine("Invalid extension format. Example: .txt");
            return;
        }

        Console.WriteLine($"Searching for *{extension} files across all drives...\n");

        foreach (var drive in DriveInfo.GetDrives())
        {
            if (drive.IsReady) // Skip drives that are not ready (e.g., empty DVD drive)
            {
                try
                {
                    SearchDirectory(drive.RootDirectory.FullName, extension);
                }
                catch (Exception ex)
                {
                    Console.WriteLine($"[Drive Error] {drive.Name}: {ex.Message}");
                }
            }
        }

        Console.WriteLine("\nSearch completed.");
    }

    /// <summary>
    /// Recursively searches directories for files with the given extension.
    /// Skips system and restricted folders.
    /// </summary>
    static void SearchDirectory(string path, string extension)
    {
        try
        {
            // Skip system folders
            string folderName = Path.GetFileName(path);
            if (folderName.Equals("System Volume Information", StringComparison.OrdinalIgnoreCase) ||
                folderName.Equals("Windows", StringComparison.OrdinalIgnoreCase))
            {
                return;
            }

            // Search files in current directory
            foreach (var file in Directory.EnumerateFiles(path, "*" + extension))
            {
                Console.WriteLine(file);
            }

            // Recursively search subdirectories
            foreach (var dir in Directory.EnumerateDirectories(path))
            {
                SearchDirectory(dir, extension);
            }
        }
        catch (UnauthorizedAccessException)
        {
            // Skip folders we can't access
        }
        catch (PathTooLongException)
        {
            // Skip overly long paths
        }
        catch (Exception ex)
        {
            Console.WriteLine($"[Error] {path}: {ex.Message}");
        }
    }
}




If you want, I can also give you a parallelized version using Parallel.ForEach to make the search much faster on multi-core CPUs.
Do you want me to prepare that?
