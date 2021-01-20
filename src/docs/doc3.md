using System;

namespace MinWeightPath
{

    class Program
    {

        static Random rnd = new Random();

        static int[,] InitializeMatrix(int n, int m)
        {

            int[,] matrix = new int[n, m];

            for (int i = 0; i < n; i++)
                for (int j = 0; j < m; j++)
                    matrix[i, j] = rnd.Next(10);

            return matrix;

        }

        static int[] GetMinimumPath(int[,] weights)
        {

            int[,] pathWeights = BuildPathWeights(weights);
            int[] path = ExtractMinimumWeightPath(weights, pathWeights);

            return path;

        }

        private static int[,] BuildPathWeights(int[,] weights)
        {

            int rowsCount = weights.GetLength(0);
            int colsCount = weights.GetLength(1);

            int[,] pathWeights = new int[rowsCount, colsCount];

            for (int i = 0; i < colsCount; i++)
                pathWeights[0, i] = weights[0, i];

            for (int row = 1; row < rowsCount; row++)
            {
                for (int col = 0; col < colsCount; col++)
                {

                    int candidateCol = col;

                    if (col > 0 &&
                        pathWeights[row - 1, col - 1] < pathWeights[row - 1, candidateCol])
                        candidateCol = col - 1;

                    if (col < colsCount - 1 &&
                        pathWeights[row - 1, col + 1] < pathWeights[row - 1, candidateCol])
                        candidateCol = col + 1;

                    pathWeights[row, col] = pathWeights[row - 1, candidateCol] + weights[row, col];

                }
            }

            return pathWeights;

        }

        private static int[] ExtractMinimumWeightPath(int[,] weights, int[,] pathWeights)
        {

            int rowsCount = weights.GetLength(0);
            int colsCount = weights.GetLength(1);

            int[] path = new int[rowsCount];

            int col = 0;
            for (int i = 1; i < colsCount; i++)
                if (pathWeights[rowsCount - 1, i] < pathWeights[rowsCount - 1, col])
                    col = i;

            int row = rowsCount - 1;
            do
            {

                path[row] = col + 1;

                if (col > 0 &&
                    pathWeights[row - 1, col - 1] + weights[row, col] == pathWeights[row, col])
                    col = col - 1;
                else if (col < colsCount - 1 &&
                    pathWeights[row - 1, col + 1] + weights[row, col] == pathWeights[row, col])
                    col = col + 1;

                row--;

            }
            while (row > 0);

            path[0] = col + 1;

            return path;

        }

        static void PrintPath(int[,] matrix, int[] path)
        {

            int rowsCount = matrix.GetLength(0);
            int colsCount = matrix.GetLength(1);

            int sum = 0;


            for (int row = 0; row < rowsCount; row++)
            {

                Console.WriteLine();

                for (int col = 0; col < colsCount; col++)
                {

                    string prefix = " ";
                    string suffix = " ";

                    if (col == path[row] - 1)
                    {

                        sum += matrix[row, col];

                        prefix = "[";
                        suffix = "]";

                    }

                    Console.Write(" {0}{1}{2} ", prefix, matrix[row, col], suffix);

                }

            }

            Console.WriteLine();
            Console.WriteLine();
            Console.WriteLine("Sum of weights: {0}", sum);
            Console.WriteLine();

        }

        static void Main(string[] args)
        {

            while (true)
            {

                Console.Write("Enter number of rows (0 to exit): ");
                int rowsCount = int.Parse(Console.ReadLine());

                if (rowsCount <= 0)
                    break;

                Console.Write("Enter number of columns: ");
                int colsCount = int.Parse(Console.ReadLine());

                int[,] matrix = InitializeMatrix(rowsCount, colsCount);

                int[] path = GetMinimumPath(matrix);

                PrintPath(matrix, path);

            }

        }
    }
}
